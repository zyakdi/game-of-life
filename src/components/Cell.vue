<template>
  <div
    class="cell"
    :class="isAlive ? 'alive' : 'dead'"
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
  background-color: #84d4da;
  opacity: 0.8;
}

.alive {
  background-color: #84d4da;
}
</style>
