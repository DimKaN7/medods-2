<template>
  <div class="main-container" :style="style">
    <Simon :steps="steps" :difficulty="difficulties[selectedDifficulty]"/>
    <div class="options">
      <span class="header">{{roundCaption}}</span>
      <button @click="onStartClick">Start</button>
      <span v-if="lostRound > 0" class="lost">{{lostCaption}}</span>
      <div class="options__difficulty">
        <span class="header">Difficulty:</span>
        <div v-for="(d, i) in difficulties" :key="i" >
          <input type="radio" :id="i" :value="i" v-model="selectedDifficulty"/>
          <label :for="i">{{d.title}}</label>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Simon from './Simon.vue';
export default {
  name: 'App',
  components: {
    Simon,
  },
  computed: {
    style() {
      return {
        height: `${this.height}px`,
      }
    },
    roundCaption() {
      return `Round: ${this.round}`;
    },
    lostCaption() {
      return `Sorry, you lost after ${this.lostRound} rounds!`;
    }
  },
  data() {
    return {
      height: window.innerHeight,
      steps: [],
      round: 0,
      lostRound: 0,
      difficulties: [
        {title: 'Easy', delay: 1.5},
        {title: 'Normal', delay: 1},
        {title: 'Hard', delay: 0.4},
      ],
      selectedDifficulty: 0,
    }
  },
  methods: {
    onStartClick() {
      this.lostRound = 0;
      this.resetGame();
      this.nextStep();
    },
    nextStep() {
      this.steps.push(Math.floor((Math.random() * 4)));
      this.round++;
    },
    resetGame() {
      this.steps = [];
      this.round = 0;
    },
    lostGame() {
      this.lostRound = this.round;
      this.resetGame();
    },
  }
}
</script>

<style lang="scss">
*, body {
  margin: 0;
  padding: 0;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  box-sizing: border-box;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  font-size: 14px;
}
.main-container {
  width: 100vw;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;

  .options {
    width: 190px;
    display: flex;
    flex-direction: column;

    .header {
      font-size: 24px;
      font-weight: bold;
      margin-bottom: 20px;
    }

    >button {
      width: 50%;
      outline: none;
      background-color: #6DABE8;
      border: none;
      border-radius: 10px;
      padding: 10px;
      margin-bottom: 20px;
      transition: background-color 0.2s ease-in-out;
      font-size: 20px;

      &:hover {
        background-color: #78BCFC;
      }
    }

    .lost {
      margin-bottom: 20px;
    }

    &__difficulty {
      width: 100%;
      display: flex;
      flex-direction: column;

      >div {
        width: 100%;
        display: flex;
        flex-direction: row;
        align-items: center;
        margin-bottom: 5px;

        &:last-child {
          margin-bottom: 0px;
        }

        >input {
          margin-right: 10px;
        }
      }
    }
  }
}
</style>
