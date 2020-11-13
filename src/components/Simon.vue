<template>
  <div class="simon">
    <div v-for="(s, i) in sectors" :key="s" 
      class="simon__sector">
      <div :style="style(s, i)" class='sector'
        ref="sector"
        @click="!disabled && onSectorClick(i)">
      </div>
    </div>
  </div>
</template>

<script>
import blueSound from '../sounds/1.mp3';
import redSound from '../sounds/2.mp3';
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
          this.lightUp(steps[i]);
          this.playAudio(steps[i]);
          i++;
          if (i >= steps.length) {
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
      }, 200);
    },
    playAudio(audioIndex) {
      this.audios[audioIndex].currentTime = 0;
      this.audios[audioIndex].play();
    },
    onSectorClick(index) {
      this.clicks.push(index);
      this.lightUp(index);
      this.playAudio(index);
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
    },
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
  -webkit-tap-highlight-color: transparent;
  position: relative;

  &::after {
    content: '';
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0;
    left: 0;
    border-radius: 50%;
    box-shadow: 0px 0px 15px -5px;
    z-index: -1;
  }

  &__sector {
    width: 50%;
    height: 50%;
    position: relative;
    overflow: hidden;

    .sector {
      width: 200%;
      height: 200%;
      position: absolute;
      top: 0px;
      left: 0px;
      border-radius: 50%;
      opacity: 0.5;
      cursor: pointer;
      border: 2px solid transparent;
      transition: all 0.2s ease-in-out;

      &:hover {
        border-color: black;
      }

      &.light {
        border-color: black;
        opacity: 1;
      }
    }
  }
}
@media (max-width: 528px) {
  .simon {
    margin-right: 0px;
    margin-bottom: 20px;

    &__sector {
      .sector {
        &:hover {
          border-color: transparent;
        }

        &.light {
          border-color: black;
          opacity: 1;
        }
      }
    }
  }
}
</style>