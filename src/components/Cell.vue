<template>
  <div
    class="cell"
    :class="{ alive: isAlive, dead: !isAlive }"
    @click="onClick"
  />
</template>

<script lang="ts">
import Vue from "vue";

export type Position = { row: number; column: number };

export default Vue.extend({
  props: {
    isAlive: { type: Boolean, required: true, default: false },
    position: {
      type: Object as () => Position,
      required: true,
      validator: (position: Position) =>
        position.row >= 0 && position.column >= 0,
    },
  },
  methods: {
    onClick: function() {
      console.log(`Click on (${this.position.row}, ${this.position.column})`);
      this.$emit("change-cell-state", this.position);
    },
  },
});
</script>

<style scoped>
.cell {
  width: 100%;
  padding-top: 100%;
}

.dead:hover {
  background-color: rgb(183, 164, 189);
}

.alive {
  background-color: rgb(114, 60, 145);
}
</style>
