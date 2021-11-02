---
title:  "Vue Caching 문제 해결하기"

categories:
  - WEB
last_modified_at: 2021-10-26T18:06:00-05:00
tags:
  - WEB
toc: true
toc_label: "Vue Caching 문제 해결하기"
---

# Caching 문제 해결하기
SPA로 웹을 개발하고 build파일을 반영하다보면 캐싱문제에 부딪히게 된다. web에서 캐싱된 파일때문에 반영을 하더라도 웹에서 캐싱을 삭제하지 않는 이상 반영된 결과를 못보는 현상이 발생한다.

# Webpack의 build.js파일을 바꿔보자
문제는 바로 web에서 network `build.js` 파일을 캐싱시키면서 적용된 파일의 결과를 볼 수 없었다.

바꾸는 것은 생각보다 굉장히 간단했다.

```javascript
//webpack.config.js 파일
output: {
    path: path.resolve(__dirname, './dist'),
    publicPath: '/dist/',
    filename: '[name].[chunkhash:8].js'  //이부분을 수정
  },
```

`filename` 부분에 chunkhash 값만 넣어서 수정하면 hash 값에 의해서 build.js파일명이 정해진다. 즉 파일이 수정되면 동적으로 파일명이 수정된다. 

`/dist` 폴더에 보면 해쉬값으로 파일이 만들어진 것을 확인할 수 있다.

![Image Alt 텍스트](/assets/img/web/caching.png)  

# index.html 파일
dist에 파일은 만들어졌지만, `index.html`에서 `dist`폴더에 만들어진 파일을 읽어야 한다. <br>
매번 달라지는 build 파일명을 index.html에 어떻게 하면 적용시킬수 있을까?<br>

## html-webpack-plugin
그래서 찾은것이  `html-webpack-plugin` 이다. <br>

webpack.config.js파일에 `html-webpack-plugin`만 추가하고 build하면 자동으로 생성된다.

```javascript
plugins:[ //추가
    new HtmlWebpackPlugin() //폴더명 추가
],
```

자동으로 생성확인!

