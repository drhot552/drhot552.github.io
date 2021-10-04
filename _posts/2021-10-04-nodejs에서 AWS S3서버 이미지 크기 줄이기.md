---
title:  "nodejs AWS S3서버 이미지 크기 줄이기(vue)"
categories:
  - Server
last_modified_at: 2021-10-04T18:06:00-05:00
tags:
  - Server
toc: true
toc_label: "nodejs AWS S3서버 이미지 크기 줄이기(vue)"
---

# vue에서 nodejs 서버로 이미지 올리기
이미지 크기는 다양하다. 때문에 이미지를 보여주는 서비스에서 이미지크기가 **3MB**이상 크기로 저장된다면, 매번 이미지를 보여줄때 서버쪽 패킷량은 점점 더 커질것이다...😅<br>
그래서 이미지크기를 줄이는 방법에 대해선 찾아보고 고민해보았다. 

먼저 VUE에 이미지 업로드를 구현하자.

# vue에 이미지 업로드 구현
html로 구현된 이미지 업로드 구현은 간단하다. 인터넷에 찾아봐도 다 나오지만, 그래도 소스코드는 공유!😀😀 (HERELUX 서비스 기준)

먼저 input태그를 이용해서 이미지 업로드 구현하자. 

## Vue코드
```html
<template>
<!-- event code -->
<div style="padding:16px;">
    <!-- 이미지 업로드 -->
    <label style="margin:0;" for="file_input_1">
        <img src="/public/img/btn_image.png" style="height: 25px; margin-bottom: 0.5rem;">
        </img>
    </label>
    <div v-for="item in this.image" class="image_style">
        <img :src="item" style="width:100%; height:100%;">
        <div class="image_delete">
            <!-- 이미지 삭제하는 버튼  -->
            <img src="/public/img/btn_close2.png" style="width:20px; z-index:0;" v-on:click="FirstImgDelete(item)" />
        </div> 
    </div>
    <div class="image-upload row">
        <!-- 이미지 업로드 input -->
        <input multiple="multiple" type="file" accept="image/*" id="file_input_1" @change="upload">
    </div>  
    <div>
        <!-- 이미지 업로드 클릭시 이벤트 -->
        <a style="color:white;" class="talkwrite_btn" v-on:click="commentWrite()">
            등록하기
        </a>
    </div>
</div>
</template>
<script>
    methods:{
        //이미지 등록 눌렀을 경우 이벤트 코드
        commentWrite(){
            axios.post( WRITEDOMAIN + '/talk/write', this.talkObject, settings)
                .then(data => {
                    console.log(data);
                    this.$router.go(-1)
                }).catch(response => {
                    console.log(response);
                    this.$router.push('/error');
            })

        }
    }
</script>

```

input에 대한 함수코드만 작성하면 된다. <br>
- commentWrite() : 글 등록시 axios를 통해서 nodejs 서버를 호출한다.
<br>
위 코드로 작성된 웹앱 페이지모습은 아래와 같다.

![Image Alt 텍스트](/assets/img/server/nodejs_image.png)


# nodejs에서 이미지 업로드 처리
서버는 AWS S3에 nodejs를 통해서 올려보자.

nodejs에 AWS S3에 올리기전에 먼저 해야될 작업이 있다. <br>
nodejs 서버가 AWS S3에 접근할 수 있는 권한이 필요하다. (없으면 꼭 해줘야합니다..!)

## AWS서버 S3 접근 권한 방법
아래 url 클릭! 하면 AWS S3 접근하는 방법이 나옵니다.

[AWS서버 S3접근 방법](https://drhot552.github.io/aws/AWS%ED%8C%8C%EC%9D%BC%EC%84%9C%EB%B2%84_S3/)


## 이미지접근 npm 모듈 설치

아래 모듈을 nodejs에서 npm으로 설치하자.

1. aws 접근 

> npm install --save aws-sdk                //aws-sdk

2. 파일 업로드 multer 모듈

> npm install --save multer                 //multer 모듈

> npm install --save multer-s3-transform    //s3에 업로드할 multer모듈

3. 파일의 용량을 줄이는 모듈

> npm install --save sharp

```javascript
/* */
const multer = require("multer");
const multerS3 = require('multer-s3-transform');
const AWS = require("aws-sdk");


AWS.config.region = 'ap-northeast-2';       //AWS에 설정된 지역 

let imgname="";
let brands_name="";

//bucket name 운영
const bucketname  = 'bucketname'; //AWS bucket name을 작성
const maxSize = 2 * 1024 * 1024; //최대 2MB로 제한

const s3 = new AWS.S3({params: {Bucket:bucketname}});

//이미지 업로드 필드 두개
var upload = multer({
    storage: multerS3({
        s3: s3,
        dirname: '/' + brands_name,
        bucket: bucketname,
        cacheControl: 'max-age=31536000',         
        acl: 'public-read-write',
        limits: {
            fileSize: maxSize,
            files: 5
        },
        ContentType:'',
        storageClass: 'REDUCED_REDUNDANCY',
        shouldTransform: function (req, file, cb) {
            cb(null, /^image/i.test(file.mimetype))
        },
        // 설정 세팅
        transforms: [{
            key: function (req, file, cb) {
                imgname = moment().format("YYYY-MM-DD") + moment().hours() + "-"  + moment().minute() + "-"  + file.originalname;
                cb(null, imgname);
            },
            transform: function(req, file, cb) {
                //Perform desired transformations
                //sharp
                cb(null, sharp()
                        .toFormat("jpeg", { mozjpeg: true })
                        );
            }
        }]
    })
});

```

multerS3의 객체에 들어가는 변수에 대해서 알아보자. 

### multers3 구문 정리
- s3 : AWS S3의 버킷네임을 지정한다.
- dirname : AWS에 저장할 폴더 이름을 지정한다.
- bucket : AWS 버킷 이름을 정한다.
- cacheControl : cache를 사용하겠다는 의미이며, max-age 31536000은 캐시 유효시간을 정하는 값이다. 
- acl : 파일접근에 대한 컨트롤을 작성한다. (ex: private 등)
- limits : 파일size와 처리할 파일의 개수를 지정한다.
- storageClass : aws s3의 스토리지 클래스를 나타낸다. 낮은 중복 스토리지(RRS, Reduced Redundancy Storage)는 매우저렴하게 데이터를 저장할 수 있는 클래스이다. (단. STORAGE보다 내구성이 떨어짐)
- ContentType : Content/mime type의 옵션을 나타낸다. default로 **multerS3.AUTO_CONTENT_TYPE** 넣는다.
- shouldTransform : 업로드 하기전에 변환값을 알려준다. 성공하면 true로 transform의 변환을 수행한다. 
- transforms : key(파일의 이름을 지정하는 값)와 transform(file의 크기와 압축을 지정하는 값) 옵션을 지정한다.

## aws s3에 업로드한 정보 받아서 처리

```javascript
router.post('/write', upload.fields([{name:"image_1"}]),function(req,res){
    let imgfile = req.files;

    console.log(imgfile);   //AWS에 업로드된 이미지 url 및 정보를 가져온다.
})
```

## 결과확인
그럼 VUE로 만든 서비스에 이미지를 올려보면 성공적으로 올라가는걸 확인할 수 있다.👌

![Image Alt 텍스트](/assets/img/server/nodejs_image_1.png)

### 업로드 후 이미지 크기
![Image Alt 텍스트](/assets/img/server/nodejs_image_2.png)

용량도 1.4MB->242.9KB 로 줄어서 AWS S3에 업로드 된걸 확인할 수 있다.



# 도움을 받았던 사이트
[multer](https://www.npmjs.com/package/multer-s3)
[cache-control](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/Cache-Control)
[storageClass](https://ultrasound.tistory.com/entry/S3Scalable-Storage-in-the-Cloud)
[multers3](https://www.npmjs.com/package/multer-s3-transform)