<template>
  <div>
    <div>당첨 숫자</div>
    <div id="result">
      <lotto-ball v-for="ball in winBalls" :key="ball" :number="ball"></lotto-ball>
    </div>
    <div>Bonus</div>
    <lotto-ball v-if="bonus" :number="bonus"></lotto-ball>
    <button v-if="redo" @click="onClickRedo">One more Time!</button>
  </div>
</template>

<script>
import LottoBall from './lottoBall';

function getWinNumbers() {
  const candidate = Array(45).fill().map((v, i) => i + 1);
  const shuffle = [];
  while (candidate.length > 0) {
    shuffle.push(candidate.splice(Math.floor(Math.random() * candidate.length), 1)[0]);
  }
  const bonusNumber = shuffle[shuffle.length - 1];
  const winNumbers = shuffle.slice(0, 6).sort((p, c) => p - c);
  return [...winNumbers, bonusNumber];
}
  const timeouts  = [];
  export default {
    components: {
      LottoBall,
    },
    data() {
      return {
        winNumbers: getWinNumbers(),
        winBalls: [],
        bonus: null,
        redo: false,
      };
    },
    methods: {
      showBalls() {
          for (let i=0; i<this.winNumbers.length - 1; ++i) {
            timeouts[i] = setTimeout(() => {
              this.winBalls.push(this.winNumbers[i]);
            }, (i + 1) * 1000);
          }
          timeouts[6] = setTimeout(() => {
            this.bonus = this.winNumbers[6];
            this.redo = true;
          }, 7000);
      },

      onClickRedo() {
        this.winNumbers = getWinNumbers();
        this.winBalls = [];
        this.bonus = null;
        this.redo = false;
      }
    },
    computed: {

    },
    mounted() {
      this.showBalls();
    },
    
    beforeDestroy() {
      timeouts.forEach((t) => {
        clearTimeout(t);
      })
    },
    watch: {
      winBalls(val, oldVal) {
        if (val.length === 0) {
          this.showBalls();
        }
      }
    }
  }
</script>

<style>
</style>
