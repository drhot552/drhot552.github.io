---
title:  "javascript android webview 함수 호출하기"
categories:
  - WEB
last_modified_at: 2021-10-05T18:06:00-05:00
tags:
  - WEB
toc: true
toc_label: "javascript android webview 함수 호출하기"
---

# 자바스크립트와 모바일간의 통신

1. 자바스크립트 인터페이스 클래스 만들기
2. WebView 에 인터페이스 클래스 등록하기
3. Javascript와 Android 함수 호출하기

## 왜사용하는지?
웹앱을 개발하여 앱과 연동하게 되면 앱의 고유 기능을 사용하는 경우가 발생한다. <br> 
(내가 사용하게된 경우는 웹 부분 로컬스토리지 초기화가 발생하여 안드로이드 로컬스토리지를 사용하기 위해 사용하게 됬다.:)<br>

- 네이버 OAuth를 사용할 경우 Web Page가 초기화 되어 localstorage도 초기화되는 문제가 발생
- 로그인 유지에 대한 고민으로 안드로이드 로컬스토리지를 사용하여 웹앱에 유지

자바스크립트와 모바일간의 통신은 어렵지 않게 할 수 있다. 하지만 처음에 할 때에는 여러모로 난관에 부딪혔다. 😅

먼저 안드로이드 간의 통신을 어떻게 하는지 코드로 살펴보자.😧

## 안드로이드 -> 자바스크립트 함수 호출
안드로이드 앱에서 -> 자바스크립트 함수를 호출하는 방법에 대해서 알아보자.

자바스크립트 코드는 매우 간단하다.

```javascript
 Echo(token){
    var arry = [];
    if(token != null && token != undefined){
    arry = token.split(',');
    localStorage.setItem('token', arry[0]);
    localStorage.setItem('id', arry[1]);
    localStorage.setItem('type', arry[2]); 
    this.$store.commit('hereluxAll/SET_USERID', arry[1]);
    }
}
```
해당코드는 안드로이드에서 받은 token 값을 웹페이지 localStorage에 값을 저장하는 코드이다. 

안드로이드 코드는 bridge를 통해서 자바스크립트의 echo함수를 호출하고 token값을 구분자 `','`로 인자값을 넘기는 코드이다.
```java
  @JavascriptInterface
        public void bridge(final String message) {
            // {"action":"callApp"}
            mHandler.post(new Runnable() {
                    public void run() {
                    if(Uri.parse(message)!= null || !Uri.parse(message).equals("")) {

                        if(message.contains("http://") || message.contains("https://")) {
                        String PACKAGE_NAME = "com.android.chrome";
                        CustomTabsIntent customTabsIntent = new CustomTabsIntent.Builder()
                                .setShowTitle(false)
                                .setToolbarColor(000000)
                                .setStartAnimations(mContext, R.anim.slide_from_rigth, R.anim.slide_to_left)
                                .setExitAnimations(mContext, R.anim.slide_from_left, R.anim.slide_to_right)
                                .build();
                        ...생략
                        }else{
                            Toast.makeText(mContext,"존재하지 않은 url 입니다.", Toast.LENGTH_SHORT).show();
                        }
                    }
                }
            });
        }
    ... 생략
    @JavascriptInterface
    public void check() {
        mHandler.post(new Runnable() {
            public void run() {
                if(PreferenceManager.getString(mContext, "token") != "" &&
                        PreferenceManager.getString(mContext, "id") != "" &&
                        PreferenceManager.getString(mContext, "type") != ""){
                    //mWebView.loadUrl(hlurl);

                    String token = PreferenceManager.getString(mContext, "token");
                    String id = PreferenceManager.getString(mContext, "id");
                    String type = PreferenceManager.getString(mContext, "type");

                    String java = "javascript:Echo('" + token
                            + "," + id
                            + "," + type
                            + "')";

                    mWebView.loadUrl(java);

                }
            }
        });
    }

```

## 자바스크립트 -> 안드로이드 호출
자바스크립트에서 안드로이드 함수를 호출하는 건 `window.android.함수명` 으로 작성하면 된다.

아래 코드는 안드로이드인지 판별하여 `navigator.userAgent.match(/herelux_app_and/i`으로 앱에서 명을 구분하여 앱인지 웹앱인지 구분하는 코드이다.

```javascript
if(navigator.userAgent.match(/Android|Tablet/i)){
    
    if(navigator.userAgent.match(/herelux_app_and/i)){
        window.android.check(); //안드로이드 check함수를 호출
        window.Echo = this.Echo;
    }
    else {
        this.$store.commit('hereluxAll/SET_WEBFLAG', true);
        if(localStorage.getItem('id')){
            this.$store.commit('hereluxAll/SET_USERID', localStorage.getItem('id'));
        }
    }
}
```

아래 check() 함수를 호출하게 되면 안드로이드 로컬스토리지 `PreferenceManager`에 저장된 값을 꺼내 자바스크립트 echo 함수로 전달한다.

```java
@JavascriptInterface
public void check() {
    mHandler.post(new Runnable() {
        public void run() {
            if(PreferenceManager.getString(mContext, "token") != "" &&
                    PreferenceManager.getString(mContext, "id") != "" &&
                    PreferenceManager.getString(mContext, "type") != ""){
                //mWebView.loadUrl(hlurl);

                String token = PreferenceManager.getString(mContext, "token");
                String id = PreferenceManager.getString(mContext, "id");
                String type = PreferenceManager.getString(mContext, "type");

                String java = "javascript:Echo('" + token
                        + "," + id
                        + "," + type
                        + "')";

                mWebView.loadUrl(java);

            }
        }
    });
}

```
## 안드로이드 로컬스토리지 사용하기

로컬스토리지는 `PreferenceManager.setString` 으로 세팅이 가능하며 마찬가지로 자바스크립트에서 호출이 가능하다.!!


### 안드로이드 코드 
```java
@JavascriptInterface
        public void token(final String token) {
            mHandler.post(new Runnable() {
                public void run() {
                    //String token = PreferenceManager.getString(mContext, "token");
                        //text = "저장된 데이터가 없습니다.";
                    PreferenceManager.setString(mContext, "token", token);

                }
            });
        }
```

### 자바스크립트 코드
```javascript
if(navigator.userAgent.match(/Android|Tablet/i)){
    if(navigator.userAgent.match(/herelux_app_and/i)){
    window.android.token(this.kakaotokenArry.access_token);
    window.android.id(this.kakaoInfo.kakao_account.email);
    window.android.type('kakao');
    }

}
````

자바스크립트 코드도 안드로이드 함수만 호출하면 끝!😮‍

간단하죠~?? 😀😀

# 도움받았던 사이트
[linuxism](https://linuxism.ustd.ip.or.kr/1112)