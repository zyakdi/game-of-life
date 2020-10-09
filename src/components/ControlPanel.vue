<template>
  <div class="container">
    <div class="speed-range">
      <img src="../assets/speed-icon.svg" alt="Leaf icon" />
      <input
        v-model.number="speedValue"
        type="range"
        :min="MIN_FREQUENCY_MS"
        :max="MAX_FREQUENCY_MS"
      />
    </div>
    <div class="cells-counter">
      <img src="../assets/leaf-icon.svg" alt="Leaf icon" />
      <p>{{ aliveCells }}</p>
      <div />
    </div>
    <div class="controls">
      <button class="play-pause-btn" :class="{red: isPlaying, green: !isPlaying}" @click="onClickPlayPause">
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
  MIN_FREQUENCY_MS?: number;
  MAX_FREQUENCY_MS?: number;
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
    return { speedValue: 1000, intervalRef: undefined, isPlaying: false };
  },
  watch: {
    speedValue: function() {
      this.onChangeRange();
    },
  },
  created() {
    this.MIN_FREQUENCY_MS = 50;
    this.MAX_FREQUENCY_MS = 1500;
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
      if (this.MIN_FREQUENCY_MS && this.MAX_FREQUENCY_MS) {
        this.intervalRef = setInterval(
          this.computeCells,
          this.MAX_FREQUENCY_MS - this.speedValue + this.MIN_FREQUENCY_MS
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
  width: 100%;
  justify-content: space-between;
  margin-top: 20px;
}

.speed-range {
  width: 40%;
  display: flex;
  flex-flow: row;
  align-items: center;
  justify-content: center;
}

.speed-range > img {
  width: 40px;
}

.cells-counter {
  height: 50px;
  width: 15%;
  border: 1px solid rgba(146, 131, 131, 0.664);
  border-radius: 20px;
  background-color: rgb(248, 246, 239);
  padding: 0 3%;
  display: flex;
  flex-flow: row;
  align-items: center;
  justify-content: space-between;
}

.cells-counter > p {
  text-align: center;
  font-size: 1.2em;
  font-weight: 500;
}

.cells-counter > img {
  width: 20px;
}

.cells-counter > div {
  width: 20px;
}

.controls {
  display: flex;
  flex-flow: row nowrap;
  justify-content: center;
  width: 40%;
}

button {
  border-radius: 70%;
  border: none;
  width: 50px;
  height: 50px;
  border: 1px solid rgba(146, 131, 131, 0.664);
  margin: 0 5px;
  background-color: rgb(248, 246, 239);
  padding: 8px 5px 5px 5px;
  outline: none;
}

button:active {
  background-color: rgb(179, 179, 179);
}

.random-btn > img {
  width: 20px;
}

.green {
  background-color: rgba(176, 243, 167, 0.849);
}

.red {
  background-color: rgb(236, 149, 149);
}

.play-pause-btn > img {
  width: 15px;
}

.next-btn > img {
  width: 15px;
}

.reset-btn > img {
  width: 15px;
}

input[type="range"] {
  height: 27px;
  -webkit-appearance: none;
  width: 30%;
  margin: 0 10px;
  background-color: transparent;
}
input[type="range"]:focus {
  outline: none;
}
input[type="range"]::-webkit-slider-runnable-track {
  width: 100%;
  height: 9px;
  cursor: pointer;
  animate: 0.2s;
  box-shadow: 0px 0px 1px #000000;
  background: #f5f7f7;
  border-radius: 21px;
  border: 0px solid #010101;
}
input[type="range"]::-webkit-slider-thumb {
  box-shadow: 0px 0px 0px #000031;
  border: 1px solid rgba(146, 131, 131, 0.664);
  height: 20px;
  width: 20px;
  border-radius: 20px;
  background: #d4d2dd;
  cursor: pointer;
  -webkit-appearance: none;
  margin-top: -6px;
}
input[type="range"]:focus::-webkit-slider-runnable-track {
  background: #f5f7f7;
}
input[type="range"]::-moz-range-track {
  width: 100%;
  height: 9px;
  cursor: pointer;
  animate: 0.2s;
  box-shadow: 0px 0px 1px #000000;
  background: #f5f7f7;
  border-radius: 21px;
  border: 0px solid #010101;
}
input[type="range"]::-moz-range-thumb {
  box-shadow: 0px 0px 0px #000031;
  border: 1px solid rgba(146, 131, 131, 0.664);
  height: 20px;
  width: 20px;
  border-radius: 20px;
  background: #9689d9;
  cursor: pointer;
}
input[type="range"]::-ms-track {
  width: 100%;
  height: 9px;
  cursor: pointer;
  animate: 0.2s;
  background: transparent;
  border-color: transparent;
  color: transparent;
}
input[type="range"]::-ms-fill-lower {
  background: #f5f7f7;
  border: 0px solid rgba(146, 131, 131, 0.664);
  border-radius: 42px;
  box-shadow: 0px 0px 1px #000000;
}
input[type="range"]::-ms-fill-upper {
  background: #f5f7f7;
  border: 0px solid #010101;
  border-radius: 42px;
  box-shadow: 0px 0px 1px #000000;
}
input[type="range"]::-ms-thumb {
  margin-top: 1px;
  box-shadow: 0px 0px 0px #000031;
  border: 1px solid rgba(146, 131, 131, 0.664);
  height: 20px;
  width: 20px;
  border-radius: 20px;
  background: #9689d9;
  cursor: pointer;
}
input[type="range"]:focus::-ms-fill-lower {
  background: #f5f7f7;
}
input[type="range"]:focus::-ms-fill-upper {
  background: #f5f7f7;
}

@media screen and (max-width: 750px) {
  .container {
    flex-flow: row wrap;
    align-items: center;
    justify-content: safe;
    margin-top: 10px;
  }

  .cells-counter {
    order: 0;
    height: 30px;
    width: 30%;
    border: 1px solid rgba(146, 131, 131, 0.664);
    border-radius: 20px;
    background-color: rgb(248, 246, 239);
    padding: 0 3%;
    display: flex;
    flex-flow: row;
    align-items: center;
    justify-content: space-between;
  }

  .cells-counter > p {
    text-align: center;
    font-size: 0.8em;
    font-weight: 400;
  }

  .cells-counter > img {
    width: 15px;
  }

  .cells-counter > div {
    width: 15px;
  }

  .controls {
    order: 1;
    width: 50%;
  }

  .speed-range {
    order: 2;
  }

  .speed-range > img {
    width: 20px;
  }

  input[type="range"] {
    -webkit-appearance: none;
    width: 60%;
    margin: 0 8px;
  }

  input[type="range"]::-webkit-slider-runnable-track {
    height: 5px;
  }

  input[type="range"]::-webkit-slider-thumb {
    box-shadow: 0px 0px 0px #000031;
    border: 1px solid rgba(146, 131, 131, 0.664);
    height: 15px;
    width: 15px;
    border-radius: 20px;
    background: #d4d2dd;
    cursor: pointer;
    -webkit-appearance: none;
    margin-top: -5px;
  }

  button {
    width: 30px;
    height: 30px;
    padding: 5px;
  }

  .random-btn > img {
    width: 12px;
  }

  .play-pause-btn > img {
    width: 10px;
  }

  .next-btn > img {
    width: 10px;
  }

  .reset-btn > img {
    width: 10px;
  }
}
</style>
