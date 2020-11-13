---
layout: post
title:  "API Gateway에서 람다로 쿼리스트링 넘겨주기"
summary: API Gateway 매핑룰 설정방법
author: coconut
date: 2020-11-13 8:30 PM
categories: [cloud]
tags: [API Gateway, lambda]
---
## Vue 컴포넌트간 통신

뷰 앱은 컴포넌트의 트리 구조로 이루어져 있습니다. 동등한 경로에 있는 컴포넌트는 부모 컴포넌트의 data나 vuex를 통해 통신할 수 있습니다. 오늘은 이벤트를 중점으로 포스팅하겠습니다.

![뷰 컴포넌트 통신 방식](/assets/img/post/vuejs6/1.png)

코드로 보면서 말씀드리겠습니다.

```vue
// Home.vue
<template>
	<GlobalNavigation></GlobalNavigation>
	<Autocomplete></Autocomplete>
	<Footer></Footer>
</template>
```

예를 들어 Autocomplete 컴포넌트는 현재 Home 컴포넌트의 자식 컴포넌트입니다. 원하는 것은 Autocomplete 컴포넌트의 select 속성을 어떻게 Home 컴포넌트의 또 다른 자식 컴포넌트에 전달할 수 있을까요? 

![image-20201111180246259](/assets/img/post/vuejs6/2.png)

조금 풀어서 설명하면, Home 컴포넌트가 Autocomplete의 select 라는 데이터를 다른 컴포넌트에게 전달해주고 싶은 경우입니다. 그러면 Autocomplete에서 특정 이벤트를 바인딩하여 상위 컴포넌트에 이벤트와 함께 select를 전달해주면 됩니다. 

```vue
// Autocomplete.vue
<template>
    <v-autocomplete
      ...
      @keydown="onKeydown"
    ></v-autocomplete>
</template>

<script>
export default {
  props: ['codes'],
  data () {
    return {
			...
      select: null,
    }
  },
  methods: {
    ...
    onKeydown (e) {
      console.log(e)
      if (e.code !== 'Enter') return
      this.$emit('onKeydown', {search: this.search})
    }
  }
}
</script>

<style scoped>

</style>
```

  ```vue
// Home.vue
<template>
	<GlobalNavigation></GlobalNavigation>
	<Autocomplete @onKeydown="search"></Autocomplete>
	<Footer></Footer>
</template>

<script>
  
  ...
    
  methods: {
    search ({search}) {
      this.keyword = search
      if (!this.keyword.length) return
      this.FETCH_RESULTS({keyword: this.keyword})
      this.$router.push(`/manual/${this.keyword}`)
    }
  }
</script>
  ```

이렇게 하면, Autocomplete 컴포넌트에서 엔터키를 입력하면 search가 이벤트와 함께 Home 컴포넌트로 전달이 됩니다. Home 컴포넌트에서는 ```<Autocomplete @onKeydown="search"></Autocomplete>``` 로 이벤트를 바인딩 한뒤 search 메소드를 호출하여 검색 루틴을 수행합니다.

## 참조

[Vue Components Communication](https://joshua1988.github.io/vue-camp/vue/components-communication.html)

[Autocomplete](https://vuetifyjs.com/en/components/autocompletes/#state-selector)



