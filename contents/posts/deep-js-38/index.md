---
title: "38. 브라우저의 렌더링 과정"
description:
date: 2024-08-04
update: 2024-08-04
tags:
  - Javascript
series: "모던 자바스크립트 Deep Dive"
---

브라우저가 HTML, CSS, JS로 작성된 텍스트 문서를 어떻게 파싱(해석)하여 브라우저에 렌더링하는지 살펴보자

#### 브라우저 렌더링 과정

1. 브라우저는 HTML, CSS, JS, 이미지, 폰트 파일 등 렌더링에 필요한 리소스를 요청하고 서버로부터 응답을 받는다.
2. 브라우저의 렌더링 엔진은 서버로부터 응답된 HTML과 CSS를 파싱하여 DOM과 CSSOM을 생성하고 이들을 결합하여 렌더 트리를 생성한다.
3. 브라우저의 자바스크립트 엔진은 서버로부터 응답된 자바스크립트를 파싱하여 AST(Abstract Syntax Tree)를 생성하고, 바이트 코드로 변환하여 실행한다. 이때 자바스크립트는 DOM API 를 통해 DOM이나 CSSOM을 변경할 수 있다. 변경된 DOM과 CSSOM은 다시 렌더트리로 결합된다.
4. 렌더트리를 기반으로 HTML 요소의 레이아웃(위치와 크기)를 계산하고 브라우저 화면에 HTML 요소를 페인팅한다.

#### 요청과 응답

브라우저의 핵심기능은 필요한 리소스(HTML,CSS, JS, Image, Font등의 정적파일 또는 서버가 동적으로 생성한 데이터)를 서버에 요청하고, 서버로부터 응답 받아 브라우저에 시각적으로 렌더링 하는 것이다.

즉, 렌더링에 필요한 리소스는 모두 서버에 존재하므로, 필요한 리소스를 서버에 요청하고 서버가 응답한 리소스를 파싱하여 렌더링 하는 것이다.

#### 주소창에 URL을 입력한 후 일어나는 과정

1. 서버에 요청을 전송하기 위해 브라우저는 주소창을 제공한다.
2. 주소창에 URL을 입력하고 엔터를 누르면, URL의 Hostname이 DNS를 통해 IP주소로 변환되고, 이 IP 주소를 갖는 서버에게 요청을 전송한다.

![](https://velog.velcdn.com/images/kys7196/post/927b93d2-b26d-4647-a014-252b713ff568/image.png)

예를 들어, 브라우저의 주소창에 https://google.com을 입력하고 엔터키를 누르면
**루트요청(/.scheme)과 Host만으로 구성된 URI에 의한 요청이 google.com 서버로 전송**된다.

루트요청에는 명확히 리소스를 요청하는 내용이 없지만, 일반적으로 서버는 루트 요청에 대해 암묵적으로 index.html을 응답하도록 기본 설정되어 있다.
즉, https://google.com/index.html 같은 요청이다.

따라서 서버는 루트요청에 대해 서버의 루트폴더에 존재하는 정적파일 index.html을 클라이언트(브라우저)로 응답한다.

만약 index.html이 아닌 다른 정적파일을 서버에 요청하려면 https://google.com/assets/data/data.json 같이 요청할 정적파일의 경로와 파일이름을 URI Host 뒤의 Path에 기술하여 서버에 요청한다. 그러면 서버는 루트 폴더의 assets/data 폴더에 있는 정적파일 data.json을 응답할 것이다.

반드시 브라우저의 주소창을 통해 서버에게 정적파일만을 요청할 수 있는 것은 아니다.
자바스크립트를 통해 서버에 정적/동적 데이터를 요청할 수도 있다. (ajax, REST API)

3. 요청한 후 네트워크 탭을 살펴보면 index.html 뿐만 아니라 CSS, js, image, font 도 응답된 것을 확인할 수 있다.

이는 브라우저의 렌더링 엔진이 HTML(index.html)을 파싱하는 도중에 외부 리소스를 로드하는 태그, 즉 CSS 파일을 로드하는 link, 이미지 파일을 로드하는 img, js를 로드하는 script 태그 등을 만나면 HTML의 파싱을 일시 중단하고 해당 리소스 파일을 서버로 요청하기 때문이다.

## HTTP, HTML 파싱과 DOM 생성, CSS 파싱과 CSSOM 생성

#### HTTP 1.1과 HTTP 2.0

HTTP는 웹에서 브라우저와 서버가 통신하기 위한 프로토콜(규약)이다.

HTTP/1.1은 기본적으로 **커넥션당 하나의 요청과 응답만 처리**한다.
즉, 여러 개의 요청을 한번에 전송할 수 없고 응답 또한 마찬가지다.

따라서 HTML 문서 내에 포함된 여러 개의 리소스 요청, 즉 CSS 파일을 로드하는 link 태그, 이미지 파일을 로드하는 img 태그, 자바스크립트를 로드하는 script 태그 등에 의한 리소스 요청이 개별적으로 전송되고 응답 또한 개별적으로 전송된다.

이처럼 HTTP/1.1은 리소스의 동시 전송이 불가능한 구조이므로
**요청할 리소스의 개수에 비례하여 응답시간도 증가하는 단점**이 있다.

![](https://velog.velcdn.com/images/kys7196/post/0bb8262b-ce47-41e4-a1bc-0550fb058e20/image.png)

**HTTP/2.0**은 커넥션당 여러개의 요청과 응답, 즉 다중 요청/응답이 가능하다.
따라서 HTTP/2.0은 여러 리소스의 동시 전송이 가능하므로 HTTP/1.1에 비해 페이지로드 속도가 약 50% 정도 빠르다고 알려져 있다.

#### HTML 파싱과 DOM 생성

HTML 문서는 문자열로 이루어진 순수한 텍스트다.
HTML 문서를 브라우저에 시각적인 픽셀로 렌더링하려면 HTML 문서를 **브라우저가 이해할 수 있는 자료구조(객체) = DOM** 로 변환하여 메모리에 저장해야 한다.

![](https://velog.velcdn.com/images/kys7196/post/76b9ade3-9879-4437-8917-cd49b2007d91/image.png)

1. 서버에만 존재하던 HTML 파일이 브라우저 요청에 의해 응답된다.
   이때 서버는 브라우저가 요청한 HTML 파일을 읽어들여 메모리에 저장한 다음
   메모리에 저장된 바이트(2진수)를 인터넷을 경유하여 응답한다.

2. 브라우저는 서버가 응답한 HTML 문서를 바이트(2진수)형태로 응답받는다.
   그리고 응답된 바이트 형태의 HTML 문서는 meta 태그의 charset 어트리뷰트에 의해 지정된 인코딩방식 (ex. UTF-8)은 content-type: text/html; charset=utf-8과 같이 응답 헤더(response header)에 담겨 응답된다. 브라우저는 이를 확인하고 문자열로 변환한다.

3. 문자열로 변환된 HTML 문서를 읽어 들여 문법적 의미를 갖는 코드의 최소 단위인 token들로 분해한다.

4. 각 토큰들을 객체로 변환하여 node들을 생성한다.
   토큰의 내용에 따라 Document, Element, Attribute, Text Node가 생성된다.
   Node는 DOM을 구성하는 기본 요소가 된다.

5. HTML 문서는 HTML 요소들의 집합으로 이루어지며 HTML 요소는 중첩관계를 갖는다.
   즉, HTML 요소의 <content></content>는 다른 HTML 요소들이 포함될 수 있다.
   HTML요소간의 모든 노드들을 **Tree 자료구조** 로 구성한다. 이 노드들로 구성된 트리 자료구조를 DOM이라 부른다.

**즉 DOM은 HTML을 파싱한 결과물이다 **

#### CSS 파싱과 CSSOM(CSS Object Model) 생성

렌더링 엔진은 HTML을 처음부터 한 줄씩 순차적으로 파싱하여 DOM을 생성해 나간다.
이처럼 렌더링 엔진은 DOM을 생성해나가다 CSS를 로드하는 `<link>` `<style>` 태그를 만나면 DOL 생성을 일시 중단한다.

그리고 `<link>`태그의 `<href>` 에 지정된 CSS 파일을 서버에 요청하여
로드한 CSS 파일이나 Style 태그 내의 CSS를 HTML과 동일한 파싱 과정 (바이트->문자-> 토큰-> 노드 -> CSSOM)을 거치며 해석하여 CSSOM을 생성한다.

이후 CSS 파싱을 완료하면 HTML 파싱이 중단된 지점부터 다시 HTML을 파싱하기 시작하여 DOM 생성을 재개한다.

```
  <!DOCTYPE html>
  <html>
    <head>
      <meta charset="UTF-8">
      <link rel="stylesheet" href="style.css">
      ...
```

렌더링 엔진은 meta 태그까지 HTML을 순차적으로 해석한 다음,
link태그를 만나면 DOM 생성을 일시 중단하고,
link태그의 href 어트리뷰트에 지정된 CSS 파일을 서버에 요청한다.

서버로부터 CSS파일이 응답되면, 렌더링 엔진은 HTML과 동일한 해석 과정(바이트 -> 문자 -> 토큰 -> 노드 -> CSSOM)을 거쳐 CSS를 파싱하여 CSSOM을 생성한다.

## 렌더 트리 생성

렌더링 엔진은 서버로부터 응답된 HTML과 CSS를 파싱하여 각각 DOM과 CSSOM을 생성한다.
그리고 DOM과 CSSOM은 렌더링을 위해 Render Tree로 결합한다.

#### 렌더트리

렌더링을 위한 트리구조의 자료구조다.
따라서 브라우저 화면에 렌더링 되지 않은 노드(ex. meta, script)와 CSS에 의해 비표시(ex. display:none)되는 노드들은 포함하지 않는다.

다시 말해, 렌더 트리는 브라우저 화면에 렌더링 되는 노드만으로 구성된다.

이후 완성된 렌더트리는 각 HTML 요소의 **레이아웃(위치와 크기)를 계산**하는 데 사용되며 **브라우저 화면에 픽셀을 렌더링 하는 페인팅 처리**에 입력된다.

**렌더트리와 레이아웃/페인트**
![](https://velog.velcdn.com/images/kys7196/post/7b40e05c-4466-4fb7-b0c1-164368520a32/image.png)

다음과 같은 경우 반복해서 레이아웃 계산과 페인팅이 재차 실행된다

- 자바스크립트에 의한 노드 추가 또는 삭제
- 브라우저 창의 리사이징에 의한 viewport 크기 변경
- HTML 요소의 레이아웃(위치, 크기)에 변경을 발생시키는 width/height, margin, padding, border, display, position, top/right/bottom/left 등의 스타일 변경

레이아웃 계산과 페인팅을 다시 실행하는 리렌더링

- 성능에 악영향을 주는 작업

#### 자바스크립트 파싱과 실행

HTML 문서를 파싱한 결과물로서 생성된 DOM은 DOM API를 제공한다.

- HTML 문서의 구조와 정보 뿐만 아니라 HTML 요소와 스타일 등을 변경 가능
- 자바스크립트 코드에서 DOM API를 사용하면 이미 생성된 DOM을 동적으로 조작할 수 있다.

CSS 파싱 과정 처럼 렌더링 엔진은 HTML을 한 줄씩 순차적으로 파싱하며 DOM을 생성해나가다가 자바스크립트 파일을 로드하는 script 태그나 js코드를 콘텐츠로 담은 script 태그를 만나면 dom 생성을 일시 중단한다.

`<script src=".js">`에 정의된 js파일을 서버에 요청하여 로드한 js파일이나 script 태그 내의 자바스크립트 코드를 파싱 -> js엔진에 제어권을 넘긴다.

JS엔진은 JS코드를 파싱하여, CPU가 이해할 수 있는 Low-level language로 변환하고 실행하는 역할을 한다.

![](https://velog.velcdn.com/images/kys7196/post/2a23c2bf-a472-4fe5-9522-675f3a0c5515/image.png)

렌더링 엔진으로부터 제어권을 넘겨받은 JS 엔진은 코드를 파싱하기 시작한다.
렌더링 엔진이 HTML, CSS 를 파싱하여 DOM, CSSOM을 생성하듯이

- 자바스크립트 엔진은 자바스크립트를 해석하여 AST를 생성하고
- AST를 기반으로 인터프리터가 실행할 수 있는 중간코드인 바이트코드를 생성하여 실행한다.

#### Tokenizing

단순한 문자열인 자바스크립트 코드를 Lexical Analysis(어휘분석) 하여 문법적 의미를 갖는 코드의 최소단위인 Token들로 분해한다.

#### Parcing

토큰들의 집합을 Syntactic Analysis(구문분석)하여 AST(Abstract Syntax Tree)를 생성한다.

AST는 토큰에 문법적의미와 구조를 반영한 트리구조의 자료구조다.

#### Bytecode 생성과 실행

파싱의 결과물로서 생성된 AST는 인터프리터가 실행할 수 있는 중간코드인 ByteCode로 변환되고 인터프리터에 의해 실행된다.

V8엔진의 경우 자주 사용되는 코드는 TurboFan이라 불리는 컴파일러에 의해 최적화된 머신코드(Optimized machine code)로 컴파일되어 성능을 최적화한다.

### 리플로우와 리페인트

자바스크립트 코드에 DOM이나 CSSOM을 변경하는 **DOM API**가 사용된 경우 DOM이나 CSSOM이 변경된다.

이때 변경된 DOM과 CSSOM은 다시 **렌더 트리로 결합**되고 변경된 렌더트리를 기반으로 **레이아웃과 페인트 과정**을 거쳐 브라우저 화면에 다시 렌더링 한다.

![](https://velog.velcdn.com/images/kys7196/post/56a571c9-d7b7-4394-a4a2-17b5f330640f/image.png)

- Reflow: Layout 계산 다시 하는 것, 노드 추가/삭제, 요소의 크기/위치 변경, 윈도우 리사이징 등 레이아웃에 영향을 주는 변경이 발생한 경우에 한하여 실행

- Repaint: 재결합된 렌더 트리를 기반으로 다시 페인트를 하는 것

### 자바스크립트 파싱에 의한 HTML 파싱 중단

렌더링 엔진과 자바스크립트 엔진은 병렬적으로 파싱을 진행하지 않고 직렬적으로 파싱을 수행한다.

이처럼 브라우저는 동기적으로, 즉 위에서 아래 방향으로 순차적으로 HTML, CSS, JS를 파싱하고 실행한다.

이것은 script 태그의 위치에 따라 HTML 파싱이 블로킹되어 DOM 생성이 지연될 수 있다는 것을 의미한다. -> **script 태그의 위치가 중요함**

위 예제의 경우 app.js의 파싱과 실행 이전까지는 DOM의 생성이 일시 중단된다.

**이때 자바스크립트 코드(app.js)에서 DOM이나 CSSOM을 변경하는 DOM API를 사용할 경우 DOM이나 CSSOM이 이미 생성되어 있어야 한다.**

```
<!DOCTYPE html>
<html>
	<head>
    	<meta charset="UTF-8">
        <link rel="stylesheet" href="style.css">
        <script>
        	const $apple = document.getElementById('apple');
            $apple.style.color = 'red';
            // TypeError: Cannot read property 'style' of null 발생.
       	</script>
    </head>
    <body>
    	<ul>
        	<li id="apple">Apple</li>
        </ul>
    </body>
</html>
```

DOM API인 document.getElementById('apple')은 DOM에서 id가 'apple'인 HTML요소를 취득한다.

하지만 document.getElementById('apple')을 실행하는 시점에는 아직 id가 'apple'인 HTML요소를 파싱하지 않았기 때문에 DOM에는 id가 'apple'인 HTML 요소가 포함되어 있지 않은 상태다.

**body 요소의 가장 아래에 자바스크립트를 위치시키는 것은 좋은 아이디어다.**

- DOM이 완성된 후에 JS가 실행되어 에러가 발생할 우려가 없다.

- 자바스크립트 로딩/파싱/실행으로 인해 **HTML 요소들의 렌더링에 지장받는 일이 발생하지 않아 페이지 로딩시간이 단축된다**.

### script 태그의 async/defer 어트리뷰트

앞에서 살펴본 자바스크립트 파싱에 의한 **DOM 생성이 중단(blocking)되는 문제를 근본적으로 해결하기 위해** HTML5부터 script 태그에 async와 defer가 추가되었다.

async, defer는 src attribute를 통해 외부 자바스크립트 파일을 로드하는 경우에만 사용할 수 있다.

즉, src 어트리뷰트가 없는 인라인 자바스크립트에는 사용할 수 없다.

```
<script async src=".js"></script>
<script defer src=".js"></script>
```

> async와 defer 를 사용하면 HTML 파싱과 외부 JS 파일의 로드가 비동기적으로 동시에 진행된다. 하지만 자바스크립트의 실행시점에 차이가 있다.

#### async attribute

- HTML 파싱과 외부 자바스크립트 파일의 로드가 비동기적으로 동시에 진행된다.
- 자바스크립트의 파싱과 실행은 자바스크립트 파일의 로드 완료된 직후 진행되며, 이때 HTML 파싱이 중단된다.

![](https://velog.velcdn.com/images/kys7196/post/e863c2b9-1d21-4eab-8294-83d33827c5c0/image.png)

- 여러개의 script태그에 async 어트리뷰트를 지정하면, script태그의 순서와는 상관없이 로드가 완료된 자바스크립트 부터 먼저 실행되므로 순서가 보장되지 않는다. (비동기)

- 따라서 순서 보장이 필요한 경우에는 지정하지 않는다.
- IE10 이상에서 지원

#### defer attribute

- async 처럼 HTML 파싱과 외부 자바스크립트 파일의 로드가 비동기적으로 동시에 진행된다.
- 단, JS의 파싱과 실행은 HTML 파싱이 완료된 직후, 즉 DOM 생성이 완료된 직후(이때 DOMContentLoaded 이벤트가 발생한다) 진행된다.

![](https://velog.velcdn.com/images/kys7196/post/96cedfde-1db2-4125-a285-8bc9432462db/image.png)

- DOM 생성이 완료된 이후 실행되어야 할 자바스크립트에 유용하다.
- defer 어트리뷰트는 IE10 이상에서 지원된다. IE6 ~ 9에서도 지원되기는 하지만 정상적으로 동작하지 않을 수 있다.
