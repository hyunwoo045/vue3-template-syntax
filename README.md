# <u>[탬플릿 문법](https://v3.ko.vuejs.org/guide/template-syntax.html#%E1%84%87%E1%85%A9%E1%84%80%E1%85%A1%E1%86%AB%E1%84%87%E1%85%A5%E1%86%B8-interpolation)</u>

위 링크 참고

## 보간법

- 이중 중괄호 구문을 두번 사용하는 문법

```html
<span>메세지: {{ msg }} </span>
```

- v-once 디렉티브를 사용하여 데이터가 변경되어도 갱신되지 않는 일회성 보간을 수행할 수 있다.
  - 같은 노드의 바인딩에도 영향을 미친다는 것은 유의하도록 한다.

```html
<span v-once> 결코 변하지 않을 것입니다: {{ msg }}</span>
```

## 원시 HTML

- 이중 중괄호는 데이터를 HTML 이 아닌 일반 텍스트로 해석하나, 실제 HTML 로 출력하려면 v-html 디렉티브를 사용해야 한다.

예제

```html
<template>
  <h1>{{ msg }}</h1>
</template>

<script>
  export default {
    data() {
      return {
        msg: '<div style="color: red;"> Hello!! </div>',
      };
    },
  };
</script>
```

- 위 코드는 msg 를 HTML 로 해석하지 않고 일반 텍스트로 해석하여 화면에 위 문자열이 그대로 출력됨
- template 태그 안의 내용을 아래와 같이 수정하여 HTML 로 인식되도록 한다.

```html
<template>
  <h1 v-html="msg"></h1>
</template>

<script>
  export default {
    data() {
      return {
        msg: '<div style="color: red;"> Hello!! </div>',
      };
    },
  };
</script>
```

## 속성

- 이중 중괄호로는 태그의 속성에 값으로 사용할 수 없다. v-bind 디렉티브를 사용한다.

```html
<template>
  <h1 v-bind:class="msg">Hello!</h1>
</template>

<script>
  export default {
    data() {
      return {
        msg: "active",
      };
    },
  };
</script>

<style>
  .active {
    font-size: 50px;
    color: royalblue;
  }
</style>
```

- v-bind 는 생략 가능하다

```html
<template>
  <h1 :class="msg">Hello!</h1>
</template>
```

- v-on 은 동적 전달인자를 전달할 때 쓰는 디렉티브이며 이 또한 생략 가능하다. 약어가 아니면 `:` 를 약어면 `@` 를 사용할 수 있다.

```html
<template>
  <h1 v-on:click='add'> {{ count }} </a>

  <h1 @click='add'> {{ count }} </a>

  <h1 @[event]='add'> ... </a>
</template>

<script>
  export default {
    data() {
      return {
        count: 1,
        attr: 'class',
        event: 'click'
      }
    }

    methods: {
      add() {
        this.count += 1
      }
    }
  }
</script>
```
