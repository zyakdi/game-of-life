<template>
  <div class="container">
    <p>There are {{ aliveCells }} alive cells</p>
    <button @click="onClickPlayPause">
      {{ isPlaying ? "Pause" : "Play" }}
    </button>
    <button @click="onClickReset">Reset</button>
    <button @click="computeCells">Next</button>
    <input
      v-model.number="speedValue"
      type="range"
      :min="MIN_SPEED"
      max="1000"
    />
    <p>value is {{ speedValue }}</p>
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
}
</style>
