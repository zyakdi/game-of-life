<template>
  <div class="container">
    <p>"There are {{ aliveCells }} alive cells"</p>
    <button>Play</button>
    <button @click="resetFunction">Reset</button>
    <button @click="playFunction">Next</button>
    <div>Slider bar</div>
  </div>
</template>

<script lang="ts">
import Vue from "vue";

type Data = { intervalValue: number; isPlaying: boolean };

export default Vue.extend({
  props: {
    aliveCells: {
      type: Number,
      required: true,
      default: 0,
      validator(value) {
        return value >= 0;
      }
    },
    resetFunction: {
      type: Function,
      required: true,
      default: function() {
        return;
      }
    },
    playFunction: {
      type: Function,
      required: true,
      default: function() {
        return;
      }
    }
  },
  data(): Data {
    return { intervalValue: 1000, isPlaying: false };
  }
});
</script>

<style scoped>
.container {
  display: flex;
  flex-flow: row nowrap;
}
</style>
