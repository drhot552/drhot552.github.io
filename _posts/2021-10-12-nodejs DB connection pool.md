---
title:  "[SERVER] nodejs mysql DB connection pool 사용하기"

categories:
  - 개발
last_modified_at: 2021-10-12T18:06:00-05:00
tags:
  - WEB
  - DB
toc: true
toc_label: "[SERVER] nodejs mysql DB connection pool 사용하기"
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


# NodeJs와 DB Connection
Nodejs에서 DB Connection Pool을 어떻게 사용하는지 이제 확인해보자.

아래코드를 보자.

```javascript
//database 연결구조
const db = require('mysql');
const config = require('../module/db_info').dev;

module.exports.conn= function(){
  const conn = db.createConnection({
    host: config.host,
    port: config.port,
    user: config.user,
    password: config.password,
    database: config.database

  });
  conn.connect(function(err) {
    if (err) {
      console.error('에러 connect:' + err.stack);
      return;
    }
    console.log('Mysql DB Connect완료! ID : ' + conn.threadId);
  });
  return conn;
  }

  //router.js
  router.get('/:brands_name', function(req, res){
      let brands_name = req.params.brands_name;
      const sql = `
        select img_url, direct_url, subtitle, brands_name, subject, event_start,event_end,event_day, content_type
        from luxury.marketing
        where use_yn = 'Y'
        and brands_name = ?
	      order by event_id desc
      `;
      conn.query(sql, brands_name ,function(err, data){
        if(err){
          console.log(err);
        } else {
          console.log(data);

          res.json(data);
        }
      })
    })
```

기존에 Nodejs에서 Mysql DB Connection은 SQL을 실행할때마다, <br>
connect()와 destroy()가 반복적으로 일어나면서 자원소모가 많이 일어나게 된다.<br>

트랜잭션이 많아지는 경우 Connect 처리를 Wait하게 되면서 서버 부하가 발생하게 된다.<br>

이를 해결하기 위해서 Connection을 Pool로 관리하여 필요할때마다 pool에서 꺼내서 connection을 맺으면 된다.


# DB Connection pool

```javascript
const conn = db.createPool({
    host: config.host,
    port: config.port,
    user: config.user,
    password: config.password,
    database: config.database,
    connectionLimit: 10,
    queueLimit: 0
  });
```
`createPool`로  `connectionLimit`을 통해 pool안에 둘 객체 수를 제한할 수 있다. 

아래코드는 db pool정보를 이용하여 SQL Query를 가져오는 module이다. 

성공시 Commit을 실패시 Rollback을 한다. (DML작업시)

```javascript
module.exports.query= async function(conn, sql, arry){
    try {
      const connection = await conn.getConnection(async conn => conn);
      try {
        /* Step 3. */
        await connection.beginTransaction();
        const [rows] = await connection.query(sql, arry);
        await connection.commit(); // COMMIT
        console.log('Query Success ');
        connection.release();
        return rows;
      } catch(err) {
        await connection.rollback(); // ROLLBACK
        console.log('Query Error : ', sql, arry, err);
        connection.release();
        return false;
      }
    } catch(err) {
      console.log('DB Error', err);
      return false;
    }
}
```

# 도움을 받았던 사이트
[connection Pool](https://ko.wikipedia.org/wiki/%EC%97%B0%EA%B2%B0_%ED%92%80)<br>
[커넥션 풀_제티위키](https://zetawiki.com/wiki/%EC%BB%A4%EB%84%A5%EC%85%98_%ED%92%80)<br>
[Connection pool을 사용함으로써 얻는 이점은 무엇일까?](https://1-7171771.tistory.com/119)<br>
[db coonnection pool](https://t-anb.tistory.com/53)