<template>
  <div class="container">
    <table>
      <tr v-for="row in rows" :key="row">
        <td v-for="column in columns" :key="column">
          <Cell
            :is-alive="board[row - 1][column - 1].isAlive"
            :position="{ row: row - 1, column: column - 1 }"
            @change-cell-state="changeCellState"
          />
        </td>
      </tr>
    </table>

    <ControlPanel />
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import Cell, { Position } from "./Cell.vue";
import ControlPanel from "./ControlPanel.vue";

type CellType = { isAlive: boolean; aliveNeighbors: number };
type Data = { columns: number; rows: number; board: CellType[][] };

export default Vue.extend({
  name: "GameBoard",
  components: { Cell, ControlPanel },
  data: (): Data => ({ columns: 10, rows: 10, board: [] }),
  created: function() {
    const board: CellType[][] = [];
    for (let i = 0; i < this.rows; i++) {
      const row: CellType[] = [];
      for (let j = 0; j < this.columns; j++)
        row.push({ isAlive: false, aliveNeighbors: 0 });
      board.push(row);
    }
    this.board = board;
  },
  methods: {
    changeCellState: function(position: Position) {
      this.board[position.row][position.column].isAlive = !this.board[
        position.row
      ][position.column].isAlive;
    },
    computeBoard: function() {}
  }
});
</script>

<style scoped>
table {
  border: 1px solid black;
  border-spacing: 0px;
  border-collapse: collapse;
}

td {
  padding: 0;
  border: 1px solid black;
}
</style>
