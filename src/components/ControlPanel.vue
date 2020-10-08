<template>
  <div class="container">
    <div class="speed-range">
      <img src="../assets/speed-icon.svg" alt="Leaf icon" />
      <input
        v-model.number="speedValue"
        type="range"
        :min="MIN_SPEED"
        max="1000"
      />
    </div>
    <div class="cells-counter">
      <p>{{ aliveCells }}</p>
    </div>
    <div class="controls">
      <button class="play-pause-btn" @click="onClickPlayPause">
        <img v-if="isPlaying" src="../assets/pause-icon.svg" alt="Pause game" />
        <img v-else src="../assets/play-icon.svg" alt="Play game" />
      </button>
      <button class="reset-btn" @click="onClickReset">
        <img src="../assets/reset-icon.svg" alt="Reset game" />
      </button>
      <button class="next-btn" @click="computeCells">
        <img src="../assets/next-icon.svg" alt="Next generation" />
      </button>
      <button class="random-btn" @click="placeRandomCells">
        <img src="../assets/random-icon.svg" alt="Random cells" />
      </button>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from "vue";

type Data = {
  speedValue: number;
  intervalRef: number | undefined;
  isPlaying: boolean;
  MIN_SPEED?: number;
  MAX_SPEED?: number;
};

export default Vue.extend({
  props: {
    aliveCells: {
      type: Number,
      required: true,
      default: 0,
      validator(value) {
        return value >= 0;
      },
    },
    resetGame: {
      type: Function,
      required: true,
      default: function() {
        return;
      },
    },
    computeCells: {
      type: Function,
      required: true,
      default: function() {
        return;
      },
    },
    placeRandomCells: {
      type: Function,
      required: true,
      default: function() {
        return;
      },
    },
  },
  data(): Data {
    return { speedValue: 500, intervalRef: undefined, isPlaying: false };
  },
  watch: {
    speedValue: function() {
      this.onChangeRange();
    },
  },
  created() {
    this.MIN_SPEED = 100;
    this.MAX_SPEED = 1000;
  },
  beforeDestroy() {
    clearInterval(this.intervalRef);
    this.intervalRef = undefined;
  },
  methods: {
    onClickPlayPause() {
      if (this.isPlaying) this.pause();
      else this.play();
    },
    onClickReset() {
      this.pause();
      this.resetGame();
    },
    play() {
      if (this.MIN_SPEED && this.MAX_SPEED) {
        this.intervalRef = setInterval(
          this.computeCells,
          this.MAX_SPEED - this.speedValue + this.MIN_SPEED
        );
        this.isPlaying = true;
      }
    },
    pause() {
      clearInterval(this.intervalRef);
      this.intervalRef = undefined;
      this.isPlaying = false;
    },
    onChangeRange() {
      if (this.isPlaying) {
        this.pause();
        this.play();
        console.log(`updating the speed`);
      }
    },
  },
});
</script>

<style scoped>
.container {
  display: flex;
  flex-flow: row nowrap;
  align-items: center;
  width: 100%;
  justify-content: space-between;
}

.speed-range {
  width: 40%;
  display: flex;
  flex-flow: row;
  align-items: center;
}

.speed-range > input {
  width: 30%;
}

.speed-range > img {
  width: 40px;
}

.cells-counter {
  width: 100px;
  height: 50px;
  border: 1px solid black;
  border-radius: 30px;
  background-color: rgb(255, 255, 255);
  width: 20%;
}

.cells-counter > p {
  text-align: center;
}

.controls {
  display: flex;
  flex-flow: row wrap;
  justify-content: center;
  width: 40%;
}

button {
  border-radius: 70%;
  border: none;
  width: 50px;
  height: 50px;
  border: 1px solid black;
  margin: 0 5px;
}

.random-btn {
}

.random-btn > img {
  width: 20px;
}

.play-pause-btn > img {
  width: 20px;
}

.next-btn > img {
  width: 20px;
}

.reset-btn > img {
  width: 20px;
}

@media screen and (max-width: 860px) {
}
</style>
