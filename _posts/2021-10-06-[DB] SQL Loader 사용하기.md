---
title:  "[DB] 오라클 SQL Loader 사용하기(엔터값포함 업로드하기)"

categories:
  - DB
last_modified_at: 2021-10-06T18:06:00-05:00
tags:
  - DB
toc: true
toc_label: "[DB] 오라클 SQL Loader 사용하기(엔터값포함 업로드하기)"
---

# DB SQL Loader
SQL Loader는 대용량 데이터를 업로드 할 때 주로 쓰는 오라클 모듈이다. 

정제된 대용량 데이터를 RDBMS에 적재할 때 사용하는데, ETL TOOL을 통해서 많이 사용하고 있다.<br>
(현재 회사에서 SQL Loader를 이용한 ETL TOOL을 통해서 사용중이다.)

SQL Loader를 사용하는건 매우 간단하다.

아래 파일을 업로드 한다고 가정해보자.

# 파일
`/Users/yunminjae/product_202108291709.csv` 의 파일은 `,`의 구분자로 데이터가 구분되어 있다. 

![Image Alt 텍스트](/assets/img/db/sql_loader1.png)  

이 파일을 SQL Loader로 올린다고 하면 어떻게 구성할 수 있을까?

먼저 구문을 알아보자.

# SQL Loader 구문
SQL Loader의 구문파일은 `test.ctl`로 만든다. 

단 꼭 ctl파일로 만들어야 한다.

```sql
OPTIONS (SKIP = 1)    -- 1
LOAD DATA             
INFILE '/Users/yunminjae/product_202108291709.csv' "STR '\r\n'"      -- 2
APPEND                -- 3
INTO TABLE 테이블명     -- 4
FIELDS TERMINATED BY '|'  -- 5
TRAILING NULLCOLS         -- 6
(
  A CHAR,
  B NUMBER
)
```

하나씩 살펴보자.

1. OPTIONS (SKIP = 1) 

 - 파일의 맨 첫줄은 제외한다. 

2. INFILE '파일경로' "STR '\r\n'" 

 - 업로드할 파일 경로를 작성한다. 전체경로를 입력.

 - STR '\r\n' 마지막 개행문자를 작성한다. 엔터값은 아래구문으로 확인할 수 있다.

  > \n - unix

  > \r - mac
  
  > \r\n - windows

3. APPEND/TRUNCATE

 - APPEND는 데이터를 추가해서 입력. TRUNCATE는 테이블 TRUNCATE 후 입력

4. INTO TABLE 테이블명

 - 테이블 명을 작성 ex) luxury.product

5. FIELDS TERMINATED BY '|' 

 - 각 필드별로 구분자를 어떤 구분자로 할 건지 작성

6. TRAILING NULLCOLS

 - 컬럼순으로 데이터 셋이 안맞을 경우 null로 채워서 그대로 입력 (단, 테이블이 NOT NULL인 조건일 경우)


# SQL Loader 실행 구문 

sqlldr를 실행하기전에 해당 서버에서는 반드시 Oracle client로 DB Connection이 가능한 환경이어야 한다.

```sql
sqlldr userid=DB유저이름/DB Passwd control=test.ctl data=product_202108291709.csv log=test.log direct=true
```

실행 구문도 하나씩 살펴보자.

1. userid

 - DB userId와 password를 작성

2. control

 - 위에서 작성한 `test.ctl` 을 입력

3. data

 - 데이터를 입력할 데이터파일

4. direct 옵션
 Direct 옵션을 사용하면 빠르게 Import 작업을 진행할 수 있다. direct 옵션을 사용하지 않게 되면 SGA 메모리를 사용하게 되며 이는 DB Wait Event 발생 위험이 있다. (convential)

5. log file 
 log file은 sqlloader 시 성공한 결과를 보여준다. 실패할 경우에는 `.bad` 파일 형식으로 남게 된다. 


# 도움을 받았던 사이트
[[Java] 줄바꿈 하기 (\n, \r, \r\n 의 차이)](https://hianna.tistory.com/602)
[[Oracle] SQL Loader 데이터 이동 사용법](https://myjamong.tistory.com/224)