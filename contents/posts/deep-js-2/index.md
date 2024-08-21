---
title: "2. 자바스크립트 개발 환경과 실행 방법"
description:
date: 2024-08-04
update: 2024-08-04
tags:
  - Javascript
series: "모던 자바스크립트 Deep Dive"
---

> 모든 브라우저는 자바스크립트를 해석하고 실행할 수 있는 자바스크립트 엔진을 내장하고 있음.
> 하지만 브라우저와 node.js 의 용도는 다름

브라우저

- HTML, CSS, JS를 실행해 웹페이지를 브라우저 화면에 렌더링
- Client-side APIs 지원
  - DOM, BOM, Canvas, XMLHttpRequest, fetch, requestAnimationFrame, SVG, Web Storage, Web Component, Web worker

Node.js

- 브라우저 외부에서 자바스크립트 실행환경을 제공
- Node.js 고유 API 제공

둘다 JS의 코어인 ECMAScript를 실행할 수는 있음 -브라우저와 Node.js에서 ECMAScript 이외에 추가로 제공하는 기능은 호환되지 않음

**웹 크롤링**
서버에서 웹사이트의 콘텐츠를 수집하기 위해 웹사이트에서 HTML 문서를 가져온 다음,
이를 가공해서 필요한 데이터만 추출하는 경우가 있음.
서버환경은 DOM API를 제공하지 않으므로 Cheerio 같은 DOM 라이브러리를 사용해 HTML 문서를 가공하기도 함

#### Node.js 와 npm

- Node.js: 크롬 V8 자바스크립트 엔진으로 빌드 된 자바스크립트 런타임 환경
- npm: 자바스크립트 패키지 매니저. Node.js에서 사용할 수 있는 모듈들을 패키지화 해서 모아둔 저장소 역할과 패키지 설치 및 관리를 위한 CLI 제공
