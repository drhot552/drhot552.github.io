---
title:  "[ERR_INVALID_ARG_TYPE]: The chunk argument must be one of type string or Buffer"

categories:
  - WEB
last_modified_at: 2021-10-19T18:06:00-05:00
tags:
  - WEB
toc: true
toc_label: "[ERR_INVALID_ARG_TYPE] The chunk argument must be one of type string or Buffer"
---

# The "chunk" argument must be one of type string or Buffer
nodejs에서 kakaodaum 검색 api를 사용하던 중 뜻하지 않는 오류가 발생했다.

![Image Alt 텍스트](/assets/img/web/nodejs_error.png)  

`[ERR_INVALID_ARG_TYPE]: The "chunk" argument must be one of type string or Buffer` 에러를 구글링해서 찾아본 결과 도움될만한 정보를 찾을 수 없다보니 nodejs에서 api응답 시 받는 데이터를 Log로 찍어보기로 하였다.

내가 구현한 코드는 아래와 같다.

```javascript
router.post('/kakaodaum', function(req, res){
  let api_url = 'https://dapi.kakao.com/v2/search/web?query=' + encodeURI(search); // json 결과
  let request = require('request');
  let options = {
      url: api_url,
      headers: { 'Authorization': 'KakaoAK xxxxxxxxxxxxxxxxxxxxx' }
    };

  request.get(options, function (error, body) {
    if (!error) {
      console.log(body.body);  //여기에 로그를찍었다.
      res.writeHead(200, {'Content-Type': 'text/json;charset=utf-8'});
      res.end(body.body);
    } else {
      res.status(500).end();
      console.log('error = ' + error);
    }
  });
})
```

결론적으로는 return 값`body`이 너무 커서 http buffer에 담을 수 없다는 오류였다.

![Image Alt 텍스트](/assets/img/web/nodejs_error_2.png)  

그래서 nodejs에서 body.body 부분만 추출해서 Return하면된다.

보통 외부 api를 사용하다보면 이런 경우가 많기 때문에 `console.log`로 확인해보면 간단하게 해결할 수 있다.


```javascript
res.end(body.body);
```

