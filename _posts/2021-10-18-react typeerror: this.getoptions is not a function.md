---
title:  "react typeerror: this.getoptions is not a function"

categories:
  - WEB
last_modified_at: 2021-10-18T18:06:00-05:00
tags:
  - WEB
toc: true
toc_label: "react typeerror: this.getoptions is not a function"
---

# typeerror: this.getoptions is not a function
react 프로젝트에서 `Sass-loader` 설정 시 `this.getoptions is not a function` 오류가 났다.

이유는 scss파일의 import시 앞부분 상대경로설정 없이 설정할 때 `yarn start` 시 오류가 났는데, 

해결하는 방법은 간단했다. 

```javascript
import 'styles/base.scss';
```

# sass-loader 버전확인
`sass-loader`를 npm이나 yarn으로 설치하게 되면 최신버전으로 설치되게 된다.<br>
버전확인은 `package.json`에서 패키지별 버전을 확인할 수 있다.

![Image Alt 텍스트](/assets/img/web/sass-loader.png)  

가장먼저 `sass-loader`와 `node-sass`의 버전을 다운그레이드 하는 것이 먼저 해야할 일이다.

`sass-loader` 버전을 10.1.0버전으로 다운그레이드 해서 재설치해야한다.

> yarn remove sass-loader

> yarn add sass-loader@10.1.0

# node-sass 버전확인
`node-sass`도 다운그레이드 해야한다.

> yarn remove node-sass

> yarn add node-sass@5.0.0

# webpack.config.js 수정
sass부분을 찾아서 `webpack.config.js` 파일도 아래와 같이 수정하자.

```javascript
{
    test: sassRegex,
    exclude: sassModuleRegex,
    use: getStyleLoaders(
    {
        importLoaders: 3,
        sourceMap: isEnvProduction
        ? shouldUseSourceMap
        : isEnvDevelopment,
    },
    'sass-loader'
    //sass-loader 여기서부터가 추가한 내용
    ).concat({
    loader: require.resolve("sass-loader"),
    options: {
        additionalData: `@import 'utils';`,
        sassOptions: {
        includePaths: [paths.appSrc + "/styles"],
        sourceMap: isEnvProduction && shouldUseSourceMap,
        },
    },
    }),
    // Don't consider CSS imports dead code even if the
    // containing package claims to have no side effects.
    // Remove this when webpack adds a warning or an error for this.
    // See https://github.com/webpack/webpack/issues/6571
    sideEffects: true,
}
```

sass-loader와 node-sass를 다운그레이드 후 webpack.config.js를 수정하면 오류가 해결된다.
