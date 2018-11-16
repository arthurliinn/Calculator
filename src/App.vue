<template>
  <div id="app">
        <input disable="disabled" :value= "result"/>
      <button v-for="(item, key) in keys " :key="key" @click="input(item)">{{ item }}</button>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      keys: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, '+', '-', '*', '/', '='],
      groupedValues: [],
      inputs: [],
    };
  },
  methods: {
    input(lable) {
      if (lable === '=') {
        this.computedResult();
        return;
      }
      this.inputs.push(lable);
      this.groupValues();
    },

    groupValues() {
      this.groupedValues= [];
      let tmp = [];
      let cachedNum;

      this.inputs.forEach((value) => {
        if (value !== '+' && value !== '-' && value !== '*' && value !=='/') {
          tmp.push(value);
        } else {
          cachedNum = Number(tmp.join(''));
          if (cachedNum) {
            this.groupedValues.push(cachedNum);
            this.groupedValues.push(value);
          }
          tmp = [];
        }
      });

      if (tmp.length > 0) {
        this.groupedValues.push(Number(tmp.join('')));
      }
    },
    computedResult() {
      let result = 0;
      let currentOp = '+';

      const temp = [];

      this.groupedValues.forEach((value, i, self) => {
        if (value === '*') {
          temp.push(temp.pop() * self[i + 1]);
          return;
        }
        if (value === '/') {
          temp.push(temp.pop() / self[i + 1]);
          return;
        }
        if (self[i - 1] === '*' || self[i - 1] === '/') { return; }
        temp.push(value);
      });

      temp.forEach((value) => {
        if (value === '+') { currentOp = value; return; }
        if (value === '-') { currentOp = value; return; }

        if (currentOp === '+') {
          result += value;
        } else if (currentOp === '-') {
          result -= value;
        }
        currentOp = '+';
      });

      // 第二種方法  遞迴

      let fn = (arr) => {
        let index = arr.indexOf('*');
        if (index < 0) {
          return arr;
        }
        let group = arr.splice(index - 1, 3);
        arr.splice(index, 0, group);
        return fn(arr);
      };

      let mp = (arr) => {
        if (arr[2] instanceof Array) {
          arr[2] = mp(arr[2]);
        }

        if (arr[1] === '*') {
          return arr[0] * arr[2];
        }
        return arr[0] + arr[2];
      };


      console.log(mp(fn(this.groupedValues.slice(0))));
      // 研究前序式
      this.groupedValues = [result];
      this.inputs = [];
    },
  },
  computed: {
    result() {
      return this.groupedValues.join(' ');
    },
  },
};
</script>

<style>
#app {
}
</style>
