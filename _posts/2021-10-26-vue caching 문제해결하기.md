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
문제는 바로 web에서 network `build.js` 파일을 캐싱시키면서 적용된 파일의 결과를 볼수 없었다.