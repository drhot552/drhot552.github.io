---
title:  "ElasticSearch cloud를 이용하여 nodejs에서 사용하기_1"
categories:
  - WEB
last_modified_at: 2021-09-10T18:06:00-05:00
tags:
  - WEB
toc: true
toc_label: "ElasticSearch cloud를 이용하여 nodejs에서 사용하기_1"
---
# ElasticSearch
ElasticSearch는 루씬 기반의 검색 엔진이다.
HTTP 웹 인터페이스와 스키마에서 자유로운 JSON 문서와 함께 분산 멀티테넌트 지원 전문 검색 엔진을 제공한다. 일래스틱서치는 자바로 개발되어 있으며 아파치 라이선스 조항에 의거하여 오픈 소스로 출시되어 있다. 

[ElasticSearch의 구조및 개념](https://www.elastic.co/guide/kr/elasticsearch/reference/current/gs-basic-concepts.html)

# ElasticSearch에 Nodejs와 연동하기
ElasticSearch Cloud를 사용해서 Nodejs와 연동해보자
가장먼저 ElasticSearch Cloud에 가입하면 30일동안 무료로 사용할 수 있다.

## ElasticSearch Cloud
![Image Alt 텍스트](/assets/img/web/elasticcloud.png)  
인덱스 생성과 조회는 elastic cloud에서 API를 통해서 쉽게 사용이 가능하다. 
- index 생성
> PUT member?pretty
![Image Alt 텍스트](/assets/img/web/elasticindex_push.png)  
- index 조회
> GET member?pretty
![Image Alt 텍스트](/assets/img/web/elasticindex_get.png)  
- index 삭제
> DELETE member?pretty
![Image Alt 텍스트](/assets/img/web/elasticindex_delete.png)  

## Nodejs 연동
ElasticCloud에서 API로 index를 직접 조회하는 것이 매우 간편하게 되어있다. 구지 코드로 조회하지 않아도 테스트가 가능하기 때문이다. 우리는 nodejs를 통해서 직접 연동해보자.
먼저 Nodejs 프로젝트에 모듈을 설치하자
> npm install --save @elastic/elasticsearch
그리고 아래 코드로 작성하면 된다.
```javascript
//module/connection.js
const { Client } = require('@elastic/elasticsearch');
const client = new Client({
  node: 'elasticsearch 주소',
  auth: {
    username: 'xxxxxx',   //elasticsearch의 주소 
    password: 'xxxxxx'
  }
})
module.exports = client;  
```
main.js에 elasticsearch의 연결정보를 가져온다.
```javascript
//main.js
const elastic = require('./module/connection.js');
elastic.search({
  index: 'product',  //elastic의 index명 (index에 product가 없다면 결과값이 나오지 않음)
  body: {
    query: {
      quote: '셀린느'   //검색할 단어 명 작성
    }
  }
}, (err, result) => {
  if (err) console.log(err)
  console.log(result);
})
```
nodejs관련 설명은 ElasticSearch 공식 홈페이지에 자세히 나와있다. 
[ElasticSearch Nodejs API](https://www.elastic.co/guide/en/elasticsearch/client/javascript-api/current/index.html)
# 참조
[Elasticsearch 개념 및 구조](https://twofootdog.tistory.com/53)
[Elasticsearch index 생성](https://velog.io/@yundleyundle/ElasticSearch-Index%EC%83%9D%EC%84%B1-%EB%B0%8F-%EC%A3%BC%EC%9D%98%ED%95%A0%EB%82%B4%EC%9A%A9)