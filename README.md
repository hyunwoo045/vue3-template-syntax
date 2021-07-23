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

# Computed

- 계산된 데이터

데이터 영역에 저장해둔 데이터를 연산을 통해서 정의한 다음 정의된 값을 반환해서 사용할 수 있는 새로운 데이터

```html
<script>
  export default {
    data() {
      return {
        fruits: ["Apple", "Banana", "Cherry"],
      };
    },

    // 미리 계산된 데이터 영역
    computed: {
      hasFruit() {
        return this.fruits.length > 0;
      },

      reverseFruits() {
        return this.fruits.map((fruit) => {
          fruit.split("").reverse().join("");
        });
      },
    },
  };
</script>
```

- methods 의 메서드를 computed 에 정의하고 여러 번 호출하면 한 번만 연산을 한다.

```html
<template>
  <h1>{{ reversedMessage() }}</h1>
  <h1>{{ reversedMessage() }}</h1>
  <h1>{{ reversedMessage() }}</h1>
  <h1>{{ reversedMessage() }}</h1>
</template>

<script>
  export default {
    data() {
      return {
        msg: "Hello!",
      };
    },
    methods: {
      reversedMessage() {
        return this.msg.split("").reverse().join("");
      },
    },
  };
</script>
```

- 위 코드는 reversedMessage 메서드의 return 연산을 총 4번 연산을 하여 결과를 출력한다.

```html
<template>
  <h1>{{ reversedMessage }}</h1>
  <h1>{{ reversedMessage }}</h1>
  <h1>{{ reversedMessage }}</h1>
  <h1>{{ reversedMessage }}</h1>
</template>

<script>
  export default {
    data() {
      return {
        msg: "Hello!",
      };
    },
    computed: {
      reversedMessage() {
        return this.msg.split("").reverse().join("");
      },
    },
  };
</script>
```

- 위 코드는 reversedMessage 함수의 return 연산을 한 번 수행한 후 그 결과를 저장해두고 4번 출력한다. 즉, 연산은 한 번만 한다.

<br/>

## Getter, Setter

기본적으로 computed 의 내용들은 읽기 전용이다. 아래와 같이 버튼을 누르면 reversedMessage 의 내용을 수정하려 해도 정상적으로 동작하지 않는 것을 볼 수 있다.

```html
<template>
  <button @click="add">ADD</button>
  <h1>{{ msg }}</h1>
</template>

<script>
  export default {
    data() {
      return {
        msg: "Hello",
      };
    },
    computed: {
      reversedMessage() {
        return this.msg.split("").reverse().join("");
      },
    },
    methods: {
      add() {
        this.reversedMessage += "!?";
      },
    },
  };
</script>
```

이를 해결하기 위해 아래와 같이 수정한다.

```html
<template>
  <button @click="add">ADD</button>
  <h1>{{ msg }}</h1>
</template>

<script>
  export default {
    data() {
      return {
        msg: "Hello",
      };
    },
    computed: {
      reversedMessage: {
        get() {
          return this.msg.split("").reverse().join("");
        },
        set(value) {
          this.msg = value;
        },
      },
    },
    methods: {
      add() {
        this.reversedMessage += "!?";
      },
    },
  };
</script>
```

- add 메서드가 동작하면 this.reversedMessage 부분에 `!?` 를 더해 재할당하면 set() 이 실행된다.
- this.msg 의 값이 바뀌면서 get() 또한 실행된다.
- 바뀐 결과값이 새로 출력된다.

## Watch

data 의 특정 값이 바뀔 경우에 메서드가 실행될 수 있도록 설정하는 옵션

```html
<template>
  <section class="box">
    <h1 class="title">Watch</h1>
    <h1 @click="changeMessage">{{ msg }}</h1>
    <h1>{{ reversedMessage }}</h1>
  </section>
</template>

<script>
  export default {
    data() {
      return {
        msg: "Hello?",
      };
    },
    methods: {
      changeMessage() {
        this.msg = "Good!";
      },
    },
    watch: {
      msg() {
        console.log(this.msg);
      },
    },
  };
</script>
```

- 웹에서 Hello? 부분을 클릭하면 Good! 으로 변경됨과 동시에 console 창에 Good! 이 출력된다.
- 변수와 같은 이름으로 설정해야 동작한다.
- computed 의 내용도 감시 가능하다.

</br>

## Class & Style Binding

https://v3.ko.vuejs.org/guide/class-and-style.html

클래스를 동적으로 전환할 수 있다.

```html
<div :class="{ active: isActive }"></div>
```

<br/>

인라인 스타일도 바인딩이 가능하다.
CSS 속성의 이름에는 camelCase 또는 kebab-case (따옴표와 함께) 사용할 수 있다.

```html
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

```javascript
data() {
  return {
    activeColor: 'red',
    fontSize: 30
  }
}
```

- 더 깔끔하게 만들기 위해서는 스타일 객체에 직접 바인딩 하는 것이 더 좋다.

```html
<div :style="styleObject"></div>
```

```javascript
data() {
  return {
    styleObject: {
      color: 'red',
      fontSize: '13px'
    }
  }
}
```

- 동적으로 스타일을 변경하는 것도 가능하다.

```html
<div :style="fontStyle" @click="changeStyle"></div>
```

```javascript
data() {
  return {
    fontStyle: {
      color: 'orange',
      fontSize: '30px',
    }
  }
}
methods: {
  changeStyle() {
    this.fontStyle.color = 'red'
  }
}
```

- 여러 스타일을 적용하기 위해 배열 구문을 활용할 수 있다.

```html
<div :style="[fontStyle, backgroundStyle]" @click="changeStyle"></div>
```

```javascript
data() {
  return {
    fontStyle: {
      color: 'orange',
      fontSize: '30px',
    },
    backgroundStyle: {
      backgroundColor: royalblue
    }

  }
}
methods: {
  changeStyle() {
    this.fontStyle.color = 'red'
  }
}
```

<br/>

## 조건부 렌더링

https://v3.ko.vuejs.org/guide/conditional.html

조건에 따라 블록을 렌더링 할 때 사용된다.

```html
<div v-if="awesome">Hello !</div>
```

- v-else 와 함께 else 블록도 함께 사용 가능하다.
- v-else-if 도 사용 가능!

```html
<div v-if="awesome">I'm Awesome!!</div>
<div v-else-if="holy">Holy !!!</div>
<div v-else>Moly !!!</div>
```

- 여러 요소들을 한번에 제어하기 위해서 아래와 같은 방법을 사용할 수 있다.

```html
<template>
  <div v-if="awesome">
    <h1>Awesome!</h1>
    <p>Javascript</p>
    <p>Vue.js</p>
  </div>
</template>
```

하지만 이와 같은 방법은 불필요한 div 요소가 렌더링 되어 최적화 측면에서 비효율적일 수 있으므로 아래와 같은 방법을 사용하는 것이 더 좋다.

```html
<template>
  <template v-if="awesome">
    <h1>Awesome!</h1>
    <p>Javascript</p>
    <p>Vue.js</p>
  </template>
</template>
```

- 실제로 element 에서 검사했을 때 template 요소는 렌더링 되지 않는 것을 확인할 수 있다.
  - 주의) 최상위 template 태그 부분에는 v-if 를 사용해서는 안 된다.
  - 전체 template 내용에 v-if 를 적용하고 싶으면 전체 template 을 한번 더 template 으로 감싸도록 하자.

<br/>

- v-if 는 조건에 맞지 않으면 렌더링 자체를 하지 않아 요소를 확인 할 수 없다.
- 조건에 맞지 않아도 렌더링이 되도록 하여 요소를 확인하고 싶다면 v-show 디렉티브를 사용하도록 한다.

```html
<template>
  <template v-show="awesome">
    <h1>Awesome!</h1>
    <p>Javascript</p>
    <p>Vue.js</p>
  </template>
</template>
```

- template 태그에는 v-show 를 적용할 수 없고, v-else 또한 지원하지 않는다.

### v-if vs v-show

- v-if 는 실제 조건부 렌더링이다. 전환 도중에 조건부 블록 내부의 이벤트 리스너 및 자식 컴포넌트들이 올바르게 제거되고 다시 생성된다.
- v-if 는 게으르다. 조건이 거짓이면 아무것도 하지 않는다. 참이 될 떄 까지 렌더링 되지 않는다.
- v-show 는 초기 조건과 관계 없이 항상 렌더링 된다. CSS 전환을 기반으로 한다. (display: none)
- 결과적으로, v-if 는 전환 비용이 높고, v-show 는 초기 렌더링 비용이 높다. 즉, 무언가를 자주 전환해야 한다면 v-show 를, 그렇지 않다면 v-if 를 사용하는 것이 더 낫다.
