---
title: "2. 자바스크립트 개발 환경과 실행 방법"
description:
date: 2024-07-31
update: 2024-07-31
tags:
  - Javascript
series: "모던 자바스크립트 Deep Dive"
---

### 3.1 자바스크립트 실행환경

---

```
브라우저 & Node.js 둘 다 자바스크립트 엔진을 내장하곤 있다.
즉, 자바스크립트는 브라우저 환경 or Node.js 환경에서 실행할 수 있다.
그러나, 브라우저와 Node.js는 용도가 다르다.
```

- `브라우저`: HTML, CSS, JS를 실행해 `웹페이지를 브라우저 화면에 렌더링`하는 것이 주 목적
- `Node.js`: `브라우저 외부에서 자바스크립트 실행환경을 제공`하는 것이 주 목적

예를 들어 `DOM APIs`

- `브라우저`: DOM APIs 지원

  - DOM, BOM, Canvas, XMLHttpRequest, fetch, requestAnimationFrame, SVG, Web Storage, Web Component, Web worker
  - HTML요소를 파싱해서 객관화한 DOM을 직접 다룬다

- `Node.js`: DOM API를 제공하지 않음

✍🏻 **웹 크롤링**

```
서버에서 웹사이트의 콘텐츠를 수집하기 위해 웹사이트에서 HTML 문서를 가져온 다음,
이를 가공해서 필요한 데이터만 추출하는 경우가 있음.
서버환경은 DOM API를 제공하지 않으므로 Cheerio 같은 DOM 라이브러리를 사용해 HTML 문서를 가공하기도 함
```
