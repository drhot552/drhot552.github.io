---
title:  "[Blockchain]블록체인의 합의구조"

categories:
  - blockchain
last_modified_at: 2019-02-28T18:06:00-05:00
tags:
  - Blockchain
  - 합의알고리즘
---


# 블록체인의 합의구조
비트코인 블록체인 하면 마이닝, 채굴, 마이너 이런 용어를 많이 들어보셨을 겁니다. 먼가 일반사람들이나 처음 블록체인을 접하는 사람들에게 있어 앞선 용어들은 무슨 광산에 있는 광부와 같은 느낌을 줍니다. 컴퓨터 공학과인 제가 처음 비트코인을 접했을 때에도 도대체 무슨 말인지 이해할 수 없었죠.

사실 Mining이라는 것을 비트코인의 합의구조를 이뤄주는 하나의 알고리즘이라고 생각하면 됩니다.(마치 매우 어려운 수학문제를 풀기 위해서 어마어마한 컴퓨팅 능력을 소모하는 것을 말하죠.)

![Image Alt 텍스트](/assets/img/mining.jpg)

마이닝이라는 단어는 비트코인으로 많이 알려져잇지만 사실은 빅데이터에서도 쓰이는 단어입니다. 즉 데이터마이닝(DataMining)이라는 용어로 해당 데이터를 분석하기 위한 과정중 하나로, 데이터가 자산인 현 시대에서는 이처럼 마이닝이라는 단어가 어떤 가치있는 자산을 얻기위한 하나의 과정을 표현하는 듯합니다.

=> 비트코인 블록체인에서의 마이닝은 결국 합의구조알고리즘(POW)이다.

# 합의구조알고리즘(POW vs POS vs DPOS)
현재 블록체인 업계에서는 크게 3가지의 합의구조가 많이 알려져 있습니다.
POW - 작업증명을 통한 합의구조(컴퓨팅
POW알고리즘은 일전에 이더리움 블록체인을 소개하면서 작업증명에 대한 알고리즘을 설명한 내용이 있습니다.
아래의 링크를 누르시면 자세한 POW알고리즘의 합의방식을 아실 수 있습니다.

[POW합의알고리즘 설명](https://drhot552.github.io/blockchain/%EC%9D%B4%EB%8D%94%EB%A6%AC%EC%9B%80%EB%B8%94%EB%A1%9D%EC%B2%B4%EC%9D%B8_%EC%9E%91%EC%97%85%EC%A6%9D%EB%AA%85%EB%B0%A9%EC%8B%9D%EB%B8%94%EB%A1%9D%EC%9D%98%EA%B5%AC%EC%A1%B0/)

## POS (Proof of Stake)
POS는 POW알고리즘의 문제점을 해결하고자 나온 합의알고리즘입니다. POW는 컴퓨팅파워의 소모가 많이들 뿐 아니라 트랜잭션 처리 속도도 느려 확장성에도 큰 문제가 발생하였습니다. 이는 POW알고리즘을 사용하는 블록체인이 가지는 문제점이었습니다. 그래서 나온 것이 바로 POS알고리즘입니다.

POS는 지분증명이라고합니다. 말그대로 지분에 따라 합의구조가 일어나는 겁니다.
그럼 이런 의문이 들수 가 있습니다. 지분이 많은 사람에 따라 합의가 일어나면 블록체인의 정체성이 흔들리는 것이 아닌가?? 저 또한 POS알고리즘의 처음 든 생각이 앞선 생각이었습니다. 하지만 그러지 못하게 많은 장치를 걸어놨어요.

1. 투표방식
민주주의 방식의 투표를 선출하여 지분증명할 노드를 선출합니다.  

제가 지금까지 블록체인 서비스를 만들기 위한 기술들을 소개하고자합니다.

>JavaScript, Nodejs, Mysql, VUE or React (HTML, CSS)

위 언어와 소프트웨어는 하나의 웹서비스를 만들 때 많이 사용되는 기술입니다. 매우 빠른 확장성을 가지고 있고, 서로간의 호환성 또한 좋습니다. 또한 블록체인 서비스 DAPP을 개발할 때에도 쉽게 적용이 가능합니다.


## JavaScript ES6
![Image Alt 텍스트](/assets/img/js.png)
### 1. for in와 for of

foreach는 오직 Array 객체에서만 사용이 가능한 메서드. (Map, Set 등에서도 지원이 가능하다) foreach의 구문인자로 Callback함수를 등록할 수 있다.
```js
var items = ['item1', 'item2', 'item3'];
items.forEach(function(item) { console.log(item); });
```
출력 결과: item, item2, item3

for in은 객체의 모든 열거가능한 속성에 대해서 반복.
for of는 객체의 []속성을 가지는 컬렉션만 반복.

```js
Object.prototype.objCustom = function () {};
Array.prototype.arrCustom = function () {};
var iterable = [3, 5, 7];
iterable.foo = "hello";
for (var key in iterable) { console.log(key); } // 0, 1, 2, "foo", "arrCustom", "objCustom"
for (var value of iterable) { console.log(value); } // 3, 5, 7
```

### 2. arrow 함수 표현방식
Arrow함수 => 문법을 사용하는 축약형 함수이다. Arrow는 표현식의 결과 값을 반환하는 표현식 본문 뿐 만 아니라 상태 블록 본문도 지원.
Ex) nums.forEach(v => { if (v % 5 === 0) fives.push(v); });

### 3. Template Strings
문법적으로 더 편하게 String을 생성할 수 있게 함.
` ` -> 이 문법을 통해서 기존 글자를 길게 사용할 수 있게 만듬

### 4. Destructuring
배열과 객체에 패턴 매칭을 통한 데이터 바인딩. 실패에 유연하며 실패 시 undefined값이 자동으로 할당 됨.
Var [a, ,b] = [1,2,3]

### 5. Let + Const
let은 선언한 블록과 그 내부 블록들에서만 유효함. const은 상수값으로 선언한 값 변경 불가.
Var는 전체외부함수

```js
function varTest() { var x = 31; if (true) { var x = 71; // same variable!
console.log(x); // 71 } console.log(x); // 71}
}}
function letTest() { let x = 31; if (true) { let x = 71; // different variable
console.log(x); // 71 } console.log(x); // 31}
}}
```

### 6. 자바스크립트의 ===와 ==의 차이점
===는 형 까지 비교하는 구문이고 ==는 단순 문자열만 비교
```js
null === undefined // false 자료형이 다르기 때문에
null == undefined // true 자료형이 다르지만 값은 빈값을 나타는 것이기 때문에
null === null // true
null = 'value' // ReferenceError // 할당하면 에러
undefined = 'value' // ‘value’ //할당하면 에러 아님
```

### 7. Modules
자바스크립트의 컴포넌트를 위한 모듈을 지원함.  
```js
export function sum(x, y) { return x + y; } export var pi = 3.141593;
import * as math from "lib/math";
var fs = require(‘fs’); <- 기존 ES5의 모듈
```

## Nodejs
자바스크립트 기반 I/O NonBlocking 단일쓰레드 서버사이드 소프트웨어 플랫폼입니다. Nodejs는 단일 스레드 방식으로 Event Callback을 통해 한꺼번에 많은 작업을 할 수 있다는 것이 특징입니다.

이오스 블록체인 서비스를 개발할 때 서버단을 Nodejs로 두었고 필요한 모듈을 npm을 이용하여 install 하는 방식으로 개발하였습니다. Node Version 은 현재 10.14.2 버전입니다.

* 장점 - Single Thread의 Non Blocking I/O의 특성을 가지고 있다. 하나의 쓰레드를 통해 이벤트 Loop를 돌면서 이벤트 큐에 추가되어 있는 작업들을 순서대로 Thread에 처리하는 방식을 사용한다. 단일 스레드로 인해서 스레드의 수에 제약을 받지 않고 cpu성능이 좋다.  
* 단점 - Javascript Callback 함수이기 때문에 Callback hell에 빠질 수 있고 그로인해 가독성이 떨어질 수 있다. 또한 단일스레드이기 때문에 Callback 함수에서 단 하나의 작업만 느리면 모든 기능들의 성능이 저하 될 수 있는 점이 문제.


[Nodejs 설명](https://plus4070.github.io/nhn%20entertainment%20devdays/Node.js_EventHandling.html)

## Mysql
흔히들 알고 계신 관계형 데이터베이스입니다. 흔히들 착각하는게 DAPP은 블록체인이 있으니 데이터베이스를 사용하지 않아도 된다라고 생각하기 쉬운데 전혀 아닙니다. 그러면 데이터베이스가 DAPP에 어떤부분에 필요한 것일까요?

보통은 트랜잭션 ID를 저장하기 위한 용도로 사용합니다. DAPP은 말그대로 탈 중앙화의 성격을 가지고 있기 때문에 최소한의 정보만을 저장해야 하기 때문에 블록체인의 Transaction ID만을 저장하는 용도로 사용합니다.

## Vue or React
현재 웹 프론트앤드 기술로 가장 뜨고 있는 자바스크립트 프레임워크 입니다. React는 페이스 북에서 나온 프레임워크 기술이고 Vue는 에반유가 직접 만든 기술입니다. React가 먼저나왔고 그 이후에 Vue 기술이 나왔습니다. 저같은 경우는 웹 프론트엔드 프레임워크를 처음다뤄보는 입장이라 Vue가 상대적으로 배우기가 쉬웠습니다.

이런 프레임워크의 가장큰 장점은 MVVM 패턴이 가능하다는 점이다. 가상돔을 활용하여 웹 프론트엔드의 성능을 높여줍니다.

![Image Alt 텍스트](/assets/img/MVVM패턴.png)

- 가상돔 기능

리액트와 뷰가 현재 사용되고 있는 기술입니다. 돔 요소가 많아지면 자바스크립트로 돔을 핸들링하는 것이 무거워집니다. 그래서 돔을 비슷한 구조로 자바스크립트를 만들고 그것을 메모리에 올려서 빠른 성능을 보여줍니다.

- 컴포넌트 기능

뷰나 리액트는 철저하게 컴포넌트 기반의 개발을 유도합니다. 즉 부모컴포넌트와 자식컴포넌트로 나눠지며 해당 돔을 컴포넌트별로 나누어서 효율적으로 개발을 할 수 있게 하는 기능입니다.

## 블록체인 DAPP구성도
이번 KISA 해커톤에서 만든 서비스 블록체인 DAPP구성도입니다. Nodejs, Javascript, Html, CSS을 이용하여 웹서비스를 만들었습니다. 사실 블록체인 DAPP구성도는 데이터베이스와 연동을 블록체인으로 바꾼것이기 때문에 구성도 자체는 큰 차이는 없습니다.

때문에 DAPP을 만들기 위해서는 웹 기술을 아는 것이 가장 중요합니다.
