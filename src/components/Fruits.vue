<template>
  <section class="box">
    <h1 class="title">
      Fruits
    </h1>
    <ul>
      <li
        v-for="fruit in fruits"
        :key="fruit">
        {{ fruit }}
      </li>
    </ul>
  </section>
  <section class="box">
    <h1 class="title">
      Reversed Fruits
    </h1>
    <ul>
      <li
        v-for="fruit in reverseFruits"
        :key="fruit">
        {{ fruit }}
      </li>
    </ul>
  </section>
  <section class="box">
    <h1 class="title">
      Getter, Setter
    </h1>
    <button @click="add">
      ADD
    </button>
    <h1>{{ reversedMessage }}</h1>
    <h1>{{ reversedMessage }}</h1>
    <h1>{{ reversedMessage }}</h1>
    <h1>{{ reversedMessage }}</h1>
  </section>
  <section class="box">
    <h1 class="title">
      Watch
    </h1>
    <h1 @click="changeMessage">
      {{ msg }}
    </h1>
    <h1>{{ reversedMessage }}</h1>
  </section>
  <section class="box">
    <h1 class="title">
      Class and Style Binding
    </h1>
    <h1
      :class="{ active: isActive }"
      @click="activate">
      Hello?!({{ isActive }})
    </h1>
  </section>
  <section class="box">
    <h1 class="title">
      Conditional Rendering
    </h1>
    <button @click="handler">
      Click Me!
    </button>
    <h1 v-if="isShow">
      Hello !!!
    </h1>
    <h1 v-else-if="count > 3">
      Count > 3
    </h1>
    <h1 v-else>
      Bye !!!
    </h1>
  </section>
  <section class="box">
    <h1 class="title">
      List Rendering
    </h1>
    <button @click="listRederingHandler">
      Click Me!
    </button>
    <ul>
      <li
        v-for="fruit in newFruits"
        :key="fruit.id">
        {{ fruit.name }} - {{ fruit.id }}
      </li>
    </ul>
  </section>
</template>

<script>
import shortid from 'shortid'
export default { 
  
  data() {
    return {
      fruits: ['Apple', 'Banana', 'Cherry'],
      msg: 'Hello?',
      isActive: false,
      isShow: true,
      count: 0,
    }
  },

  computed: {
    hasFruit() {
      return this.fruits.length > 0
    },
    reverseFruits() {
      return this.fruits.map((fruit) => {
        return fruit.split("").reverse().join("");
      });
    },
    reversedMessage: {
      get() {
        return this.msg.split('').reverse().join('');
      },
      set(v) {
        this.msg = v;
      },
    },
    newFruits() { 
      return this.fruits.map((fruit)=> {
        return {
          id: shortid.generate(),
          name: fruit,
        }
      })
    }
  },
  methods: {
    add() {
      this.reversedMessage += '!?'
    },
    changeMessage() {
      this.msg = 'Good!'
    },
    activate() {
      this.isActive = !this.isActive
    },
    handler() {
      this.isShow = !this.isShow
      this.count += 1
    },
    listRederingHandler() {
      this.fruits.push('Orange')
    }
  },

  watch: {
    msg() {
      console.log('msg: ', this.msg)
    }
  }
}
</script>

<style scoped>
body {
  font-size: 20px;
}
.box {
  margin-bottom: 50px;
}
.box .title {
  font-size: 35px;
}
.active {
  color:red;
  font-weight: bold;
}
</style>