---
title:  "Logstash 이용하여 mysql와 ElasticSearch 연동하기"
categories:
  - WEB
last_modified_at: 2021-09-18T18:06:00-05:00
tags:
  - WEB
toc: true
toc_label: "Logstash 이용하여 mysql와 ElasticSearch 연동하기"
---

# Logstash
RDBMS로 운영중인 서비스를 어떻게 하면 엘라스틱서치와 연동하여 데이터를 사용할 수 있을 지 고민하였다. <br>
연동방법에 대해 Elasticsearch를 찾아보던 중 Logstash라는 모듈이 있었다.

엘라스틱서치 공식홈페이지에서는 Logstash라는 녀석을 아래와 같이 정의하고 있다.

> 데이터 집계, 변환, 저장

로그 데이터를 다루는데 적합한 녀석이지만 나는 데이터베이스에서 가져오는 걸로 사용목적이랑 조금 다르게 사용했다.<br>


[Logstash의 개념](https://www.elastic.co/kr/logstash/)

# Logstash와 Mysql와 ElasticSearch 연동하기
그럼 Logstash와 Mysql을 어떻게 연동할 수 있을까? <be>
많은 구글링 끝에 logstash와 mysql 연동하는 걸 정리해본다.

## Logstash 구동 서버 (AWS EC2)
먼저 Logstash를 구동하기 위한 AWS EC2서버를 하나 만들자.

중요한건 꼭 16.03LTS 서버로 설치하자. 나중에 mysql-connector-java-5.1.38.jar 설치시 18.04버전 이후부터는 mysql jdbc 설치가 안된다. 

> EC2 Ubuntu 16.03LTS 서버

Logstash를 설치하기 전에 리눅스에 자바를 먼저 설치해야 한다.

- 자바 설치

> sudo apt-get update

> sudo apt-get install openjdk-8-jdk

단 자바는 8버전으로 설치해야 하고 JAVA_HOME, PATH를 설정한다.

> export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java

> export PATH=$PATH:$JAVA_HOME/bin

- logstash 설치

> sudo apt-get install logstash

[Logstash 설치방법](https://www.elastic.co/guide/kr/logstash/5.3/installing-logstash.html)

logstash를 설치하면 아래와 같이 경로가 생성된다.

```javascript
/usr/share/logstash/bin/ 
```
![Image Alt 텍스트](/assets/img/web/logstash_1.png)  

## mysql jdbc 드라이버 설치하기

Mysql을 서비스하기 때문에 mysql의 jdbc 드라이버가 필요하다. 때문에 꼭 설치를 해야한다.

>  mysql-connector-java

AWS EC2리눅스 서버이기 때문에 MavenProject사이트에 다운을 받지 못해 명령어로 다운로드 설치를 해야한다.

> sudo apt-get install libmysql-java

> cd /usr/share/logstash/bin/

> mv /usr/share/java/mysql-connector-java-5.1.38.jar  .

mysql-connector-java-5.1.38.jar를 다운받으면 꼭 /usr/share/logstash/bin/ 폴더에 이동시켜야 한다.

![Image Alt 텍스트](/assets/img/web/logstash_2.png)  


## logstash와 mysql와 elasticsearch연동하기

- mysql과 연동할 conf 파일 생성
conf 파일은 /home/ubuntu 디렉토리에 설정하였다.

### db_info.conf
```javascript
input { 
  jdbc { 
    jdbc_driver_library=>"/usr/share/logstash/tools/mysql-connector-java-5.1.38.jar" jdbc_driver_class=>"com.mysql.jdbc.Driver" 
    jdbc_validate_connection=>true 
    jdbc_connection_string=>"jdbc:mysql://luxurydb.cd43xs6awqmk.ap-northeast-2.rds.amazonaws.com:3306/luxury?useSSL=false" 
    jdbc_user=>"xxxxx" 
    jdbc_password=>"xxxx" 
    schedule => "0 */6 * * * *" 
    statement=>"select * from luxury.product"} 
    }
output { 
  elasticsearch { 
    hosts=>["https://herelux.es.us-west1.gcp.cloud.es.io:9243"]
    index=>"product" 
    document_id=>"%{id}" 
    user => "xxxxx" 
    password => "xxxxxxx" 
    } 
    stdout {} 
  }
```

- input
구문은 JSON 형식의 input, output으로 구성되어있으며 input은 mysql의 db 정보를 입력

- output
elasticsearch cloud 정보를 입력하면 된다.

### input 구문 정리
- jdbc_driver_library : RDBMS의 jdbcdriver 위치
- jdbc_driver_class : mysql driver 
- jdbc_validate_connection : 연결여부 체크 확인 
- jdbc_connection_string : mysql 연결정보 (aws rdbms 연결정보)
- jdbc_user : mysql user 정보
- jdbc_password : mysql password
- scedule : logstash 스케줄 실행 (crontab과 동일하다.)
- statement : sql 구문

### output 구문 정리
- host : elasticsesarch host명 작성
- index : elasticsearch의 index명 작성
- document : elasticsearch의 구분 document (보통 rdbms의 테이블 키값을 정한다)
- user / paswword  : elastcisearch 의 유저명과 패스워드 입력

# logstash 백그라운드로 실행
생성한 conf파일을 실행하면 logstash를 이용하여 mysql에서 데이터를 읽어와 elasticsearch에 index를 생성하고 정보를 입력한다. 

> cd /usr/share/logstash/bin/ 

> ./logstash -f /home/ubuntu/db_info.conf &


![Image Alt 텍스트](/assets/img/web/logstash_3.png)  
