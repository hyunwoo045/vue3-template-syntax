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
</template>

<script>
export default { 
  
  data() {
    return {
      fruits: ['Apple', 'Banana', 'Cherry'],
      msg: 'Hello?'
    }
  },

  computed: {
    hasFruit() {
      return this.fruits.length > 0
    },
    reverseFruits() {   // 여러 번 호출되더라도 여러 번 연산하지 않는다.
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
    }
  },
  methods: {
    add() {
      this.reversedMessage += '!?'
    },
    changeMessage() {
      this.msg = 'Good!'
    }
  },

  watch: {
    msg() {
      console.log('msg: ', this.msg)
    }
  }
}
</script>

<style>
body {
  font-size: 20px;
}
.box {
  margin-bottom: 50px;
}
.box .title {
  font-size: 35px;
}

</style>