---
title:  "vue store module화 하기"
categories:
  - WEB
last_modified_at: 2021-10-02T18:06:00-05:00
tags:
  - WEB
toc: true
toc_label: "vue store module화 하기"
---
# vue store
Vue를 이용하여 서비스 개발하다보면 컴포넌트간에 변수 사용이 많아지게 된다. <br>
특히 부모와 자식간의 컴포넌트간 변수공유가 아닌 전혀다른 컴포넌트 간의 변수공유에는 store가 필수적으로 필요하다.

그래서 store를 이용하여 데이터 값 <mark>state</mark> <mark>mutations</mark> <mark>actions</mark>을 사용하는 경우가 많아짐에 따라 store에 저장된 변수 및 함수를 하나의 store에 저장하면 코드가 복잡해지는 문제가 생긴다.

코드가 길어지고 복잡해지는 로직이 들어갈 수록 <mark>store</mark> 관리가 어렵고 복잡해진다. <br>
이를 각각 js 파일로 나누어서 모듈화 하는 방법을 알아보자.

**ps. vue 공식홈페이지에 자세히 나와있지만 이해가 잘 되지 않아서 직접 찾아서 구현한 걸 소개한다.**

# 초기 vue store
처음 vue로 서비스를 개발하다보면 아래와 같이 하나의 <mark>store/index.js</mark>파일에 값을 저장하여 사용한다.

```javascript
// store/index.js
import * as api from '../../api';
import router from '../../router';

//state 값
const state = {
  userId : String,
  token : String,
  type : String,
  .... }

// mutations
const mutations = {
  IDX_INCREMENT (state) {
    state.idx++
  },
  BRAND_IDX_INCREMENT(state){
    state.brandList_idx++
  }
  ....
}
// actions
const actions = {
  FETCH_RANK_READMORE({commit, state}){....}
  ...
}
```

코드만 보면 얼마 안되는데? 라고 생각할 수 있지만, 실제 **상태, 변이, 액션**을 많이 사용하면 걷잡을 수 없이 커지게 된다.
이렇게 만든 store는 아래와 같이 매우 간단하게 사용할 수 있다.

```javascript
  data() {
    return {
      userId : ''
    }
  }
//vue 컴포넌트에서 vuex store 사용시
created(){
  //state 사용
  this.userId = this.$store.state.userId;
  //mutations 사용
  this.$store.commit('IDX_INCREMENT');
  //actions 사용
  this.$store.dispatch('FETCH_RANK_READMORE');
  .....
}
```

# vue store 모듈화
vue 공식 홈페이지 보면 <mark>index.js</mark>에 아래와 같이 모듈화 하는 방법이 나온다.
하지만 나는 store의 값을 파일별로 모듈화 시키고 싶었다.

```javascript
const moduleA = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})
....

```

## 파일별로 모듈화 시키기 
store 폴더에 있는 <mark>index.js</mark>파일을 업무별로 여러개의 파일로 나눈다.

(검색, 상품, 게시판 등등으로 나눴다.)

![Image Alt 텍스트](/assets/img/web/store.png)  

각 파일에 작성되는 상태, 변이, 액션의 내용들은 모듈화 하기전 내용과 같다. 단 맨 아래 <mark>namespaced</mark>를 꼭 <mark>true</mark>로 지정해야하고, action, mutations, actions를 export default 해준다.

```javascript
// store/module/searchList.js 
const state = {

},
const mutations = {

},
const actions = {

}
//namespaced : 각 파일별로 네임스페이스를 지정하여, 해당 파일의 상태, 변이, 액션을 사용하겠다고 명시하는 문구입니다.
export default {namespaced: true, state, mutations, actions}
```

파일을 다 나누면 store/index.js에는 각 모듈화 파일을 가져오면 된다.
```javascript
import Vuex from 'vuex'
import Vue from 'vue'
import hereluxAll from './modules/hereluxAll'
import brandList from './modules/brandList'
import searchList from './modules/searchList'
import talkList from './modules/talkList'
import product from './modules/product'
import writeboard from './modules/writeboard'
import boardList from './modules/boardList'
import myList from './modules/myList'
import home from './modules/home'


Vue.use(Vuex)

const store = new Vuex.Store({
  modules :{
    hereluxAll,
    brandList,
    searchList,
    talkList,
    product,
    writeboard,
    boardList,
    myList,
    home
  }
})

export default store
```

## 모듈화 된 store 값 사용하기
모듈화 된 store 값을 사용하는 건 간단하다. 

### store의 state 사용
<mark>computed</mark>에 <mark>...mapState</mark>를 사용하여 module한 파일명과 store의 상태값을 가져올 변수명을 적으면 된다.

```javascript
computed: {
      ...mapState('hereluxAll', { //store 모듈화한 file명
        webFlag: 'webFlag'    //store 변수명 
      }),
      ...mapState('product',{
        id : 'id',
        name : 'name',
        brand_name : 'brand_name',
        category_middle_name : 'category_middle_name',
        category_large_name : 'category_large_name'
      }),
      ...mapState('talkList',{
        talkmodalShow : 'talkmodalShow',
        talkmodalId : 'talkmodalId',
        talkmodalDescript : 'talkmodalDescript' ,
        talkmodalTitle : 'talkmodalTitle',
        talkmodalGubun : 'talkmodalGubun',
        talkmodalcommentId : 'talkmodalcommentId'
      })
  }
```

### store의 mutation사용

### store의 action사용

# 도움을 받았던 사이트
[vuex 모듈](https://vuex.vuejs.org/kr/guide/modules.html)
