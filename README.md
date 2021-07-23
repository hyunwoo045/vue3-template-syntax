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

<br/>

### v-if vs v-show

- v-if 는 실제 조건부 렌더링이다. 전환 도중에 조건부 블록 내부의 이벤트 리스너 및 자식 컴포넌트들이 올바르게 제거되고 다시 생성된다.
- v-if 는 게으르다. 조건이 거짓이면 아무것도 하지 않는다. 참이 될 떄 까지 렌더링 되지 않는다.
- v-show 는 초기 조건과 관계 없이 항상 렌더링 된다. CSS 전환을 기반으로 한다. (display: none)
- 결과적으로, v-if 는 전환 비용이 높고, v-show 는 초기 렌더링 비용이 높다. 즉, 무언가를 자주 전환해야 한다면 v-show 를, 그렇지 않다면 v-if 를 사용하는 것이 더 낫다.

<br/>

## 리스트 렌더링

일반적인 리스트 렌더링

````html
<ul>
  <li v-for="(fruit, index) in fruits" :key="fruit">
    {{ fruit }} - {{ index + 1 }}
  </li>
  ```
</ul>
````

<br/>
computed 의 내용도 리스트 렌더링이 가능하다.

```html
<template>
  <ul>
    <li v-for="fruit in newFruits" :key="fruit.id">{{ fruit.name }}</li>
  </ul>
</template>
```

```javascript
data() {
  return {
    fruits: ['Apple', 'Banana', 'Cherry']
  }
}
computed: {
  newFruits() {
    return this.fruits.map((fruits, idx) => {
      return {
        id: idx,
        name: fruit
      }
    })
  }
}
```

### shortid 패키지를 이용하여 id 를 생성

```bash
$ npm i -D shortid
```

```html
<script>
  import shortid from "shortid";

  export default {
    computed: {
      newFruits() {
        return this.fruit.map((fruit) => ({
          id: shortid.generate(), // 고유 id 생성
          name: fruit,
        }));
      },
    },
  };
</script>
```

- 객체 구조 분해

```html
<ul>
  <li v-for="{id, name} in newFruits" :key="id">{{ name }}-{{ id} }</li>
</ul>
```

<br/>

### 배열 변경 감지

[Detail](https://v3.ko.vuejs.org/guide/conditional.html)

배열의 변이 메소드로 인해 데이터가 변경되면 Vue 가 감시하고 있다가 뷰 갱신한다. 래핑된 메서드는 아래와 같다

- push()
- pop()
- shift()
- unshift()
- splice()
- sort()
- reverse()

원본 배열을 변경하는 메소드들이며 `filter()`, `concat()`, `slice()` 와 같이 원본을 변경하지 않는 메소드들은 할당 연산을 통해서 배열을 교체하여, 변경된 내용이 화면에 출력될 수 있도록 할 수 있다.

```javascript
this.fruit = this.fruit.filter((fruit) => fruit.match(/Apple/));
```

이 때 Vue 는 기존 DOM 을 버리고 전체를 다시 렌더링 하는 것이 아님.

<br/><br/>

## 이벤트 핸들링

```html
<button @click="addCounter">Click Me!</button>
<p>카운터 입니다! {{ count }} 번 클릭 되었습니다.</p>
```

```javascript
data() {
  return {
    count: 0
  }
}
method: {
  addCounter() {
    this.count += 1
  }
}
```

- v-on 디렉티브를 이용하여 메소드를 호출할 때에 인자를 넘기는 것도 가능하다
- 이 때 v-on 디렉티브내에서 인자를 넘길 때 `$event` 를 명시하여 event 객체를 넘길 수 있다.

```html
<button @click="handler('hi', $event)">Click!</button>
```

```javascript
methods: {
  handler(msg, event) {
    console.log(msg)
  }
}
```

<br/>

- 여러 개의 메소드를 호출 할 수 있다.
- 이 때 넘길 인자가 없더라도 `()` 소괄호를 열고 닫아줘야 한다.

```html
<button @click="handlerA(), handlerB()">Click!</button>
```

```javascript
methods: {
  handlerA() {
    console.log('A');
  },
  handlerB() {
    console.log('B');
  }
}
```

<br/><br/>

## 이벤트 수식어

### - prevent

event 의 기본 동작을 막는 방법으로 `preventDefault()` 를 사용할 수 있다. <br/>
Vue 에서는 아래와 같이 v-on 디렉티브에 `.`(마침표) 뒤에 prevent 를 명시함으로써 이벤트의 기본 동작을 막을 수 있다.

```html
<a href="https://naver.com" target="_blank" @click.prevent="handler"></a>
```

- once : 특정 이벤트가 발생했을 때 메소드를 단 한 번만 실행되도록 해주는 수식어

```html
<a href="https://naver.com" target="_blank" @click.once="handler"></a>
```

- 체이닝으로 여러개의 수식어를 사용할 수 있다.

```html
<a href="https://naver.com" target="_blank" @click.prevent.once="handler"></a>
```

<br/>

### - stop

- 아래 코드는 이벤트 버블링 (Event Bubbling) 에 의해 child 를 클릭하면 parent 도 클릭하는 것으로 인식되어 B 가 출력되고 A 도 출력되게 된다.

```html
<div class="parent" @click="handlerA">
  <div class="child" @click="handlerB"></div>
</div>
```

```javascript
export default {
  methods: {
    handlerA() {
      console.log("A");
    },
    handlerB() {
      console.log("B");
    },
  },
};
```

```html
<style scoped lang="scss">
  .parent {
    width: 200px;
    height: 100px;
    background-color: royalblue;
    margin: 10px;
    padding: 10px;
    .child {
      width: 100px;
      height: 100px;
      background-color: orange;
    }
  }
</style>
```

- 이를 `stopPropagation()` 메소드를 이용하여 이벤트 전파를 막을 수 있다
- Vue 는 `stop` 이벤트 수식어로 이벤트 전파를 막을 수 있다.

```html
<div class="parent" @click="handlerA">
  <div class="child" @click.stop="handlerB"></div>
</div>
```

- child 요소에 click 이벤트 뒤에 stop 수식어를 추가한다.

```javascript
export default {
  methods: {
    handlerA() {
      console.log("A");
    },
    handlerB(event) {
      // event.stopPropagation()
      console.log("B");
    },
  },
};
```

```html
<style scoped lang="scss">
  .parent {
    width: 200px;
    height: 100px;
    background-color: royalblue;
    margin: 10px;
    padding: 10px;
    .child {
      width: 100px;
      height: 100px;
      background-color: orange;
    }
  }
</style>
```

<br/>

### - capture

- 이벤트 버블링은 자식 요소를 클릭 했을 때 자식 요소의 이벤트가 먼저 발생하고 부모 요소의 이벤트가 발생한다.
- 이벤트 캡쳐링 (Event Capturing) 은 이를 반대로 동작하도록 하는 기능이다.

```html
<div class="parent" @click.capture="handlerA">
  <div class="child" @click="handlerB"></div>
</div>
```

- 캡쳐링을 적용한 상태에서 자식 요소로의 이벤트 전파를 막을 수도 있다.

```html
<div class="parent" @click.capture.stop="handlerA">
  <div class="child" @click="handlerB"></div>
</div>
```

<br/>

### - self

- 기본적으로 부모 요소 위에 있는 자식 요소를 클릭하더라도 부모 요소의 이벤트가 잘 동작한다.
- 자식 요소가 아닌 정확한 부모 요소의 영역을 클릭해야만 이벤트가 발생할 수 있도록 하기 위해서는 `self` 수식어를 사용한다.

```html
<div class="parent" @click.self="handlerA">
  <div class="child"></div>
</div>
```

### - passive

이벤트의 로직과 화면의 움직임을 독립시켜 준다.

아래 코드는 스크롤을 할 때 마다 event 를 10,000번씩 출력하는 코드이다.

```html
<template>
  <div class="parent" @wheel="handler">
    <div class="child"></div>
  </div>
</template>

<script>
  export default {
    methods: {
      handler(event) {
        for (let i = 0; i < 10000; i++) {
          console.log(event);
        }
      },
    },
  };
</script>

<style scoped lang="scss">
  .parent {
    width: 200px;
    height: 100px;
    background-color: royalblue;
    margin: 10px;
    padding: 10px;
    overflow: auto;

    .child {
      width: 100px;
      height: 2000px;
      background-color: orange;
    }
  }
</style>
```

- 이 때, 실제 웹에서 스크롤을 해보면 스크롤이 버벅이는 현상을 체감할 수 있다. 화면의 움직임과 로직을 웹이 동시에 처리하고 있기 때문이다.
- 이 둘을 분리 시켜 주기 위해 이벤트 수식어로 `passive` 를 명시하여 화면의 움직임이 빠르게 동작할 수 있도록 할 수 있다.

```html
<template>
  <div class="parent" @wheel.passive="handler">
    <div class="child"></div>
  </div>
</template>

<script>
  export default {
    methods: {
      handler(event) {
        for (let i = 0; i < 10000; i++) {
          console.log(event);
        }
      },
    },
  };
</script>

<style scoped lang="scss">
  .parent {
    width: 200px;
    height: 100px;
    background-color: royalblue;
    margin: 10px;
    padding: 10px;
    overflow: auto;

    .child {
      width: 100px;
      height: 2000px;
      background-color: orange;
    }
  }
</style>
```

## 이벤트 - 키 수식어

키보드 입력에 대하여 아래와 같이 핸들링 할 수 있다.

```html
<template>
  <input type="text" @keydown="handler" />
</template>

<script>
  export default {
    methods: {
      handler(event) {
        if (event.key === "Enter") {
          console.log("Enter!!!");
        }
      },
    },
  };
</script>
```

이 내용을 Vue 는 키 수식어로 간편하게 핸들링 할 수 있다.

```html
<template>
  <input type="text" @keydown.enter="handler" />
</template>

<script>
  export default {
    methods: {
      handler(event) {
        console.log("Enter!!!");
      },
    },
  };
</script>
```

체이닝도 가능하다. `Ctrl + A` 를 입력하면 `Enter!!!` 가 출력되는 내용이다.

```html
<template>
  <input type="text" @keydown.ctrl.a="handler" />
</template>

<script>
  export default {
    methods: {
      handler(event) {
        console.log("Enter!!!");
      },
    },
  };
</script>
```

<br/>

## 폼 입력 바인딩

아래와 같은 데이터 출력 형식을 <u>단방향 데이터 바인딩 연결</u>이라고 말하며, `input` 태그 내에 내용을 수정하더라도 `h1` 태그의 내용을 수정되지 않는 것을 볼 수 있다.

```html
<template>
  <h1>{{ msg }}</h1>
  <input type="text" :value="msg" />
</template>

<script>
  export default {
    data() {
      return {
        msg: "Hello World!",
      };
    },
  };
</script>
```

반응성을 통해서 양방향 데이터 바인딩을 구현하기 위해 아래와 같이 수정한다.

```html
<template>
  <h1>{{ msg }}</h1>
  <input type="text" :value="msg" @input="handler" />
</template>

<script>
  export default {
    data() {
      return {
        msg: "Hello World!",
      };
    },
    methods: {
      handler(event) {
        console.log(event.target.value);
        this.msg = event.target.value;
      },
    },
  };
</script>
```

아래와 같이 인라인 방식으로 구현도 가능하다.

```html
<!-- 인라인 방식 -->
<template>
  <h1>{{ msg }}</h1>
  <input type="text" :value="msg" @input="msg = $event.target.value" />
</template>

<script>
  export default {
    data() {
      return {
        msg: "Hello World!",
      };
    },
  };
</script>
```

아래와 같이 더 축소도 가능하다. v-model 이라는 디렉티브를 사용하는 것이다.

```html
<!-- v-model 사용 -->
<template>
  <h1>{{ msg }}</h1>
  <input type="text" :value="msg" v-model="msg" />
</template>

<script>
  export default {
    data() {
      return {
        msg: "Hello World!",
      };
    },
  };
</script>
```

- v-model 을 사용할 때에 주의할 점은 한글을 입력할 때 글자 하나가 모두 완성되고 넘어가야 한 글자에 대한 변경점이 적용된다.
- input 태그에서 한글을 입력 받을 때는 v-model 을 사용하지 않도록 한다.

<br/>

## v-model

- `input` 태그 내에 내용이 변경되면 즉시 msg 의 값이 변경되는 코드

```html
<!-- 인라인 방식 -->
<template>
  <h1>{{ msg }}</h1>
  <input type="text" :value="msg" @input="msg = $event.target.value" />
</template>

<script>
  export default {
    data() {
      return {
        msg: "Hello World!",
      };
    },
  };
</script>
```

위 코드에 input 이벤트를 change 로 변경하면 tab 키나 enter 키를 입력해야 변경점이 적용되도록 할 수 있다.

```html
<!-- change event -->
<template>
  <h1>{{ msg }}</h1>
  <input type="text" :value="msg" @change="msg = $event.target.value" />
</template>

<script>
  export default {
    data() {
      return {
        msg: "Hello World!",
      };
    },
  };
</script>
```

위 코드를 v-model 로 적용하려면 `lazy` 라는 수식어를 사용한다.

```html
<!-- v-model lazy -->
<template>
  <h1>{{ msg }}</h1>
  <input type="text" v-model.lazy="msg" />
</template>

<script>
  export default {
    data() {
      return {
        msg: "Hello World!",
      };
    },
  };
</script>
```

입력 란에 숫자만을 입력받아야 한다면 `number` 수식어를 이용한다

```html
<!-- v-model number -->
<template>
  <h1>{{ msg }}</h1>
  <input type="text" v-model.number="msg" />
</template>

<script>
  export default {
    data() {
      return {
        msg: 123,
      };
    },
  };
</script>
```

앞 뒤 공백을 없애기 위해서는 `trim` 수식어를 이용한다. <br/>
입력란을 수정되고 blur 될 때 trim 이 적용된다.

```html
<template>
  <h1>{{ msg }}</h1>
  <input type="text" v-model.trim="msg" />
</template>

<script>
  export default {
    data() {
      return {
        msg: "hello world!",
      };
    },
  };
</script>
```
