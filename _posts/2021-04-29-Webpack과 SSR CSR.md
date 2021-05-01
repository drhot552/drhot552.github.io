---
title:  "[Web] Webpack과 SSR, CSR"

categories:
  - WEB
last_modified_at: 2021-04-29T18:06:00-05:00
tags:
  - WEB
toc: true
toc_label: "Webpack과 SSR, CSR"
---

# WebPack
WebPack이란?
Webpack은 의존 관계에 있는 모듈들을 하나의 자바스크립트 파일로 번들링하는 모듈 번들러입니다.

![Image Alt 텍스트](/assets/img/web/webpack.png)  

Webpack을 알기 전 SSR과 CSR 부터 알아볼 필요가 있습니다.

# 순서는 아래와 같다.
## SSR은 ServerSideRendering 의 줄임말
SSR을 사용하면 모든 데이터가 매핑된 서비스 페이지를 클라이언트(브라우저)에게 바로 보여줄 수 있습니다.
서버를 이용해서 페이지를 구성하기 때문에 클라이언트에서 구성하는 CSR(client-side rendering)보다 페이지를 구성하는 속도는 늦어지지만 전체적으로 사용자에게 보여주는 콘텐츠 구성이 완료되는 시점은 빨라진다는 장점이 있습니다. 더불어 SEO(search engine optimization) 또한 쉽게 구성할 수 있습니다.

![Image Alt 텍스트](/assets/img/web/ssr.png)  

### 장점

- SEO(검색엔진)에 최적화되어 있습니다. 이 문제가 가장 큰 문제인데, React와 Vue는 이를 개선하고자 Nextjs와 Nuxtjs로 SSR에 대한 가이드를 주고 있습니다.
- 초기 구동 속도가 빠릅니다.

### 단점

- 페이지 이동마다 서버에 요청하기 때문에 많은 서버 비용이 발생한다.
- 화면 깜빡임이 발생하여 사용자 UX가 떨어집니다.

# CSR
## CSR은 ClientSideRendering 의 줄임말
SSR보다 초기 전송되는 페이지의 속도는 빠르지만 서비스에서 필요한 데이터를 클라이언트(브라우저)에서 추가로 요청하여 재구성해야 하기 때문에 전제적인 페이지 완료 시점은 SSR보다 느려집니다.

![Image Alt 텍스트](/assets/img/web/csr.png)  

### 장점

- 첫 로딩에 모든 파일(HTML, static)을 다 다운받으면 그 이후 렌더링은 매우 친화적인 ux를 제공할 수 있습니다.
- 서버에 요청하는 횟수가 그만큼 적기 때문에 요청하는 비용이 적게듭니다.

### 단점

- SEO(검색엔진) 문제가 발생합니다. 구글을 제외하고 크롤링이 안되는 문제가 발생합니다.
- 첫 구동이 느립니다. (첫 HTML과 Static) 파일들을 받느라 시간이 오래걸립니다. (때문에 Chuck를 통해서 bundle 사이즈를 최대한 줄입니다.)

그래서 Webpack이 왜 필요한건데?
SSR, CSR 자바스크립트 만의 거대한 프로젝트로 인하여 우린 이러한 자바스크립트 파일과 CSS, Static 파일을 하나로 묶어 관리할 필요가 생기게 되었습니다.

파일 단위의 자바스크립트 모듈 관리의 필요성
웹 개발 작업 자동화 도구 (Web Task Manager)
웹 애플리케이션의 빠른 로딩 속도와 높은 성능

파일 단위의 자바스크립트 모듈 관리의 필요성
전통적인 HTML 코드를 보면 app.js와 main.js를 script태그를 감싸주어 참조하고있습니다.

```javascript
<!-- index.html -->
<html>
  <head>
    <!-- ... -->
  </head>
  <body>
    <!-- ... -->
    <script src="./app.js"></script>
    <script src="./main.js"></script>
  </body>
</html>
```

이러한 파일들을 하나로 묶어주는 역할을 합니다.

# 웹 개발 작업 자동화 도구 (Web Task Manager)
웹 서비스를 개발하고 웹 서버에 배포할 때 아래와 같은 작업들을 해야 했습니다. 때문에 자동으로 아래의 작업들을 배포해 주는 역할이 필요했습니다.

- HTML, CSS, JS 압축
- 이미지 압축
- CSS 전처리기 변환

# 웹 애플리케이션의 빠른 로딩 속도와 높은 성능
가장 중요한 부분이 아닐까 싶습니다.
Webpack은 CSS, Js 등의 여러파일들을 압축해서 기본적으로 필요한 자원은 미리 로딩하지 않고 그 때 그 때 요청해 로딩 속도를 높였습니다.

### 참조
[네이버 D2 어서 와, SSR은 처음이지?](https://d2.naver.com/helloworld/7804182)
[Front-End 면접 질문 대비 Part4(SSR, CSR, Execution Context, Iterator, Event Delegation)](https://velog.io/@holim0/Front-End-%EB%A9%B4%EC%A0%91-%EC%A7%88%EB%AC%B8-%EB%8C%80%EB%B9%84-Part4)
[웹팩의 등장 배경](https://joshua1988.github.io/webpack-guide/motivation/why-webpack.html#%EC%9B%B9%ED%8C%A9%EC%9D%98-%EB%93%B1%EC%9E%A5-%EB%B0%B0%EA%B2%BD)



