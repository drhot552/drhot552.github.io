---
title:  "[Web]Ubuntu Nginx구성하기(vue)"

categories:
  - WEB
last_modified_at: 2021-04-30T18:06:00-05:00
tags:
  - WEB
toc: true
toc_label: "[Web]Ubuntu Nginx구성하기(vue)"
---

# Nginx
Nginx이란?
웹 서버 소프트웨어로, 가벼움과 높은 성능을 목표로 한다. 웹 서버, 리버스 프록시 및 메일 프록시 기능을 가진다.

엔진엑스(Nginx)는 Igor Sysoev라는 러시아 개발자가 동시접속 처리에 특화된 웹 서버 프로그램이다. Apache보다 동작이 단순하고, 전달자 역할만 하기 때문에 동시접속 처리에 특화되어 있다.

동시접속자(약 700명) 이상이라면 서버를 증설하거나 Nginx 환경을 권장한다고 한다. 지금은 아파치가 시장 점유율이 압도적(?)이지만, 아마존웹서비스(AWS) 상에서는 시장 점유율 44%에 달할정도로 가볍고, 성능이 좋은 엔진이라고 한다.

# Nginx를 사용하는 이유?
개인 프로젝트에서 Nginx를 사용한 이유는 강력한 웹서버 모니터링과 안정성 때문이다. 

기존에 Vue를 기동했을 때는 npm run dev? 로 백그라운드에서 실행시켜 쉽게 앱서비스가 죽는 현상이 발생하여 도입하였다. 

# Nginx의 기본적인 역할

## 정적 파일을 처리하는 HTTP 서버로서의 역할
웹서버의 역할은 HTML, CSS, Javascript, 이미지와 같은 정보를 웹 브라우저(Chrome, Iexplore, Opera, Firefox 등)에 전송하는 역할을 한다.

## 응용프로그램 서버에 요청을 보내는 리버스 프록시로서의 역할
두번째 역할은 리버스 프록시(reverse proxy)인데, 한마디로 말하면 클라이언트는 가짜 서버에 요청(request)하면, 프록시 서버가 배후 서버(reverse server)로부터 데이터를 가져오는 역할을 한다. 여기서 프록시 서버가 Nginx, 리버스 서버가 응용프로그램 서버를 의미한다.

# Ubuntu EC2 Nginx 설치하기
- sudo apt-get update
- sudo apt-get install nginx

Nginx를 설치하면 아래 폴더 및 파일들이 생긴다.
- /etc/nginx

```javascript
├── conf.d
├── fastcgi.conf
├── fastcgi_params
├── koi-utf
├── koi-win
├── mime.types
├── nginx.conf
├── proxy_params
├── scgi_params
├── sites-available
│   └── default
├── sites-enabled
│   └── default -> /etc/nginx/sites-available/default
├── snippets
│   ├── fastcgi-php.conf
│   └── snakeoil.conf
├── uwsgi_params
└── win-utf
```

/etc/nginx/sites-available/default 파일을 열면 아래 server 부터 작성된 부분을 수정하면된다.

```javascript
server {
  //포트번호
	listen 8080 default_server;
	listen [::]:8080 default_server;

  //웹서비스를 실행시킬 build 경로
	root /home/ubuntu/real/luxury_dev/;

  //ip 및 호스트 주소
	server_name hereluxury.com;
	
  //경로 설정
	location / {
            root   /home/ubuntu/real/luxury_dev/;
            try_files $uri $uri/ @rewrites;
        }
        location @rewrites {
                rewrite ^(.+)$ /index.html last;
        }
}
```

## 코드 블럭 작성
- server {} : 도메인 단위의 1차 라우팅 (https://hereluxury.com이 이에 해당한다.)
- location {} : 도메인 내부의 2차 라우팅 (https://hereluxury.com/이 location 라우팅에 해당한다.)

### server 블럭 내 구문들
- server_name : server 블록에서 어떤 도메인을 라우팅 할 것인가가 이에 해당한다.
- access_log / error_log : 서버에 대한 로그를 남긴다.
- root : server_name에 해당하는 도메인이 있을 때, root 폴더를 지정할 수 있습니다. reverse forwading의 경우 생략된다.
  (vue의 경우 npm run build로 해서 나온 경로를 설정하면됩니다. ex)dist 폴더) - 기본적으로는 /var/www/default 에 설정하기도 한다.
- index : 어떤 파일을 index 파일로 지정할 것인가를 설정합니다. reverse forwading의 경우 생략된다.
- location 블럭 내 구문들
try_files $uri $uri/ =404; : nginx는 정적 파일 호스팅을 기본적으로 지원하지 않기에, root 폴더 내에 uri 에 따른 폴더가 있는지 찾아보고, 만약 없다면 404 에러를 보여준다.
- @rewrites : 재작성(Rewrite)이란 요청을 통해서 주어진 URL의 규칙을 변경해서 웹서비스를 보다 유연하게 만드는 방법이다.  rewrite 모듈을 통해서 URL을 재작성 할 수 있다. 

## VUE 프로젝트를 Nginx에 적용
먼저 Vue 프로젝트의 build 파일을 만든다.

> npm run build

그럼 아래와 같이 /dist 폴더에 build 파일들이 만들어진다.
![Image Alt 텍스트](/assets/img/web/nginx1.png)  


![Image Alt 텍스트](/assets/img/web/nginx2.png)  

그리고 /etc/nginx/sites-available/default 파일에서 아래 경로를 작성하면된다.

```javascript
server {
  //포트번호 => 서버 포트번호를 적는다.
	listen 8080 default_server;
	listen [::]:8080 default_server;

  //웹서비스를 실행시킬 build 경로 /dist까지 적어줄 필요가 없었다. 적어주니 제대로 파일을 읽히지 않는 문제가 발생
	root /home/ubuntu/real/luxury_dev/;

  //ip 및 호스트 주소
	server_name hereluxury.com;
	
  //경로 설정
	location / {
            root   /home/ubuntu/real/luxury_dev/;
            try_files $uri $uri/ @rewrites;
        }
        location @rewrites {
                rewrite ^(.+)$ /index.html last;
        }
}
```

## Nginx의 로그 
Nginx는 아래 경로에 로그가 쌓인다.

/var/log/nginx의 경로
![Image Alt 텍스트](/assets/img/web/nginx3.png)  



## Nginx 삭제하기
nginx의 설정을 잘못하는 경우 아래 순서대로 명령어를 실행하면 삭제된다.
- sudo apt-get remove nginx nginx-common # config files을 제외하고 삭제
- sudo apt-get purge nginx nginx-common # 전체다 삭제
- sudo apt-get autoremove #종속성 관련 페키지 삭제
- rm -rf /etc/nginx




# 참조
[Nginx 이해하기 및 기본 환경설정 세팅하기](https://whatisthenext.tistory.com/123)
[TIL - nginx 라우팅에 대하여 알아보자](https://velog.io/@pinot/nginx-%ED%8F%AC%ED%8A%B8%ED%8F%AC%EC%9B%8C%EB%94%A9%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90)



