---
title:  "[SERVER] nodejs DB connection pool 사용하기"

categories:
  - WEB
  - DB
last_modified_at: 2021-10-12T18:06:00-05:00
tags:
  - WEB
  - DB
toc: true
toc_label: "[SERVER] nodejs DB connection pool 사용하기"
---

# Connection Pool?
연결 풀 또는 커넥션 풀(connection pool)은 단어 두개로 나눠서 보면 간단히 이해할 수 있다.

`Connection(연결), Pool(집합체)` 

Connection을 모아놓은 집합체로 이해하면 된다. 쉽죠? 😃

## Connection Pool은 왜 사용해야 할까?

사용하는 가장 큰 이유는 DB에 부하를 줄이기 위함이다.<br>
사용자가 Application에서 DB Connection을 사용하고 해제하지 않는다면 비용적인 측면에서 DB에서 큰 성능 저하를 일으키게 된다.<br>
그래서 Pool을 만들어 필요할때 Pool에서 사용하고 사용이 다 완료되면 Pool에 반납한다.

![Image Alt 텍스트](/assets/img/db/connectionpool.jpeg)  


# NodeJs와 DB Connection pool
Nodejs에서 DB Connection Pool을 어떻게 사용하는지 이제 확인해보자.



# 도움을 받았던 사이트
[connection Pool](https://ko.wikipedia.org/wiki/%EC%97%B0%EA%B2%B0_%ED%92%80)<br>
[커넥션 풀_제티위키](https://zetawiki.com/wiki/%EC%BB%A4%EB%84%A5%EC%85%98_%ED%92%80)<br>
[Connection pool을 사용함으로써 얻는 이점은 무엇일까?](https://1-7171771.tistory.com/119)