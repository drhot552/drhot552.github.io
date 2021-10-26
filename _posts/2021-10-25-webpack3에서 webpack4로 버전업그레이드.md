---
title:  "webpack3에서 webpack4로 업그레이드"

categories:
  - WEB
last_modified_at: 2021-10-25T18:06:00-05:00
tags:
  - WEB
toc: true
toc_label: "webpack3에서 webpack4로 업그레이드"
---

# Webpack3 to Webpack4
웹팩 버전을 바꾸려고 했던 이유는 프로젝트를 빌드할 때 `npm run build`로 나오는 `build.js` 파일명을 바꾸는 것에서 부터 시작됬다.

`nginx`로 vue 서비스하면서 변경된 부분을 반영을 했는데 제대로 되지 않아 원인을 찾아보니....

web에서 `build.js`를 캐싱하고 있었던 것!!

![Image Alt 텍스트](/assets/img/web/webpack3_build.png)  


그래서 `html-webpack-plugin`와 `clean-webpack-plugin` 이 두 모듈을 이용해서 `index.html`파일을 자동으로 생성하면서 `build.js`명을 hash 값으로 만들어주려고 했다.

하지만.. `webpack3`에서는 제대로 동작을 하지 않아 `webpack4`로 버전 업그레이드하기로 결정!


# Webpack4로 바꾸기
`package.json`의 버전을 백업시키자. 혹시 모를 오류와 복구를 못할 가능성 이 있기 때문에, 전체 파일 백업을 해야한다.!!

```javascript
//package.json
 "webpack": "^3.6.0",
 "webpack-bundle-analyzer": "^3.9.0",
 "webpack-dev-server": "^2.9.1"
 ....
```

> npm uninstall webpack

현재 webpack은 5버전까지 나와있다. 나는 버전4를 먼저 업그레이드 시키기 위해서 4.44.2버전으로 설치했다.
 
> npm install --save webpack@4.44.2

그리고 실행! 
> npm run dev

당연히 오류가났다.
```javascript
Error: Cannot find module 'webpack/bin/config-yargs'
Require stack:
- /Users/yunminjae/project/luxury_dev/node_modules/webpack-dev-server/bin/webpack-dev-server.js
```

구글링으로 찾아보니 npm으로 실행시 `webpack-dev-server`의 버전 문제였다. 2.9.1에서 3이상의 버전으로 업그레이드 시켜줘야한다.<br>
`webpack-dev-server`의 3버전은 `webpack-cli`로 webpack을 실행시키기 때문에 webpack-cli로 같이 설치해줘야한다.

> npm install webpack-dev-server@3

> npm install webpack-cli

마찬가지로 `package.json`에서 실행시키는 아래와 같이 `webpack serve`로 구문도 바꿔줘야한다. 

```javascript
//package.json
"scripts": {
    "dev": "cross-env NODE_ENV=development webpack serve",
    "build": "cross-env NODE_ENV=production webpack --mode production"
},
```

다 했다 생각하고 다시 실행시켰지만 아래와 같은 빨간색 오류가 미친듯이 나오기 시작했다.

![Image Alt 텍스트](/assets/img/web/webpack3_error.png)  

오류내용을 보니 `vue-loader`에서 문제가 생긴듯 하다.<br>
그래서 `vue-loader`의 버전을 마찬가지로 버전 업그레이드를 했다.

> npm install vue-loader@14.2.2

업그레이드 후 `npm run build` 및 `npm run dev` 하면 이상없이 실행 또는 build되는 것을 확인할 수 있었다!!

![Image Alt 텍스트](/assets/img/web/webpack3_error_2.png)  


# 도움을 받았던 사이트
[webpack 3에서 webpack 5로 업그레이드](https://velog.io/@seonpark/webpack-3%EC%97%90%EC%84%9C-webpack-5%EB%A1%9C-%EC%97%85%EA%B7%B8%EB%A0%88%EC%9D%B4%EB%93%9C)<br>
