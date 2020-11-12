<template>
  <div class="simon">
    <div v-for="(s, i) in sectors" :key="s" class="simon__sector">
      <div :style="style(s, i)" class='sector'
        ref="sector"
        @click="!disabled && onSectorClick(i)">
      </div>
    </div>
  </div>
</template>

<script>
import blueSound from '../sounds/2.mp3';
import redSound from '../sounds/1.mp3';
import yellowSound from '../sounds/3.mp3';
import greenSound from '../sounds/4.mp3';

export default {
  name: 'Simon',
  props: {
    steps: {
      type: Array,
      required: true,
    },
    difficulty: {
      type: Object,
      required: true,
    }
  },
  methods: {
    style(color, index) {
      let translate = '';
      switch (index) {
        case 0:
          break;
        case 1:
          translate = 'translate(-50%, 0)';
          break;
        case 2:
          translate = 'translate(0, -50%)';
          break;
        case 3:
          translate = 'translate(-50%, -50%)';
          break;
        default:
          break;
      }
      return {
        backgroundColor: `${color}`,
        transform: translate,
      }
    },
    showSteps(steps) {
      if (!this.interval) {
        this.disabled = true;
        let i = 0;
        this.interval = setInterval(() => {
          try {
            this.lightUp(steps[i]);
            this.audios[steps[i]].play();
            i++;
          } catch (e) {
            clearInterval(this.interval);
            this.interval = null;
            this.disabled = false;
            return;
          }
        }, this.difficulty.delay * 1000); 
      }
    },
    lightUp(sectorIndex) {
      this.$refs.sector[sectorIndex].classList.add("light");
      setTimeout(() => {
        this.$refs.sector[sectorIndex].classList.remove("light");
      }, 300);
    },
    onSectorClick(index) {
      this.clicks.push(index);
      this.audios[index].play();
      this.lightUp(index);
      if (this.steps[this.clicks.length - 1] === index) {
        if (this.clicks.length === this.steps.length) {
          this.clicks = [];
          this.$parent.nextStep();
        }
      }
      else {
        this.clicks = [];
        this.disabled = true;
        this.$parent.lostGame();
      }
    }
  },
  data() {
    return {
      sectors: ['blue', 'red', 'yellow', 'green'],
      audios: [
        new Audio(blueSound),
        new Audio(redSound),
        new Audio(yellowSound),
        new Audio(greenSound),
      ],
      clicks: [],
      interval: null,
      disabled: true,
    }
  },
  watch: {
    steps: function() {
      if (this.steps.length > 0) {
        this.showSteps(this.steps);
      }
    }
  }
}
</script>

<style lang="scss">
.simon{
  width: 300px;
  height: 300px;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  margin-right: 20px;

  &__sector {
    width: 50%;
    height: 50%;
    position: relative;
    overflow: hidden;
    rotate: 180deg;

    .sector {
      width: 200%;
      height: 200%;
      position: absolute;
      top: 0px;
      left: 0px;
      border-radius: 50%;
      opacity: 0.6;
      cursor: pointer;
      border: 2px solid transparent;
      transition: all 0.2s ease-in-out;

      &:hover {
        // opacity: 1;
        border-color: black;
      }

      &.light {
        opacity: 1;
      }
    }
  }
}
</style>