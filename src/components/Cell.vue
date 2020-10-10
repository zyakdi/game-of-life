<template>
  <div
    class="cell"
    :class="{ alive: isAlive, dead: !isAlive }"
    @mouseenter="onMouseEnter"
    @mousedown="onMouseDown"
  />
</template>

<script lang="ts">
import Vue from "vue";

export type Position = { row: number; column: number };

export default Vue.extend({
  name: "Cell",
  props: {
    isAlive: { type: Boolean, required: true, default: false },
    position: {
      type: Object as () => Position,
      required: true,
      validator: (position: Position) =>
        position.row >= 0 && position.column >= 0,
    },
    isMouseDown: { type: Boolean, required: true, default: false },
  },
  methods: {
    onMouseEnter(): void {
      if (this.isMouseDown) this.$emit("change-cell-state", this.position);
    },
    onMouseDown(): void {
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
