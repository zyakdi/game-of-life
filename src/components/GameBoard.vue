<template>
  <div class="game-board">
    <table>
      <tr v-for="row in rows" :key="row">
        <td
          v-for="column in columns"
          :key="column"
          :class="{
            alive: board[row - 1][column - 1].isAlive,
            dead: !board[row - 1][column - 1].isAlive,
          }"
        >
          <Cell
            :is-alive="board[row - 1][column - 1].isAlive"
            :position="{ row: row - 1, column: column - 1 }"
            @change-cell-state="changeCellState"
          />
        </td>
      </tr>
    </table>

    <ControlPanel
      :alive-cells="getAliveCells()"
      :reset-game="resetBoard"
      :compute-cells="computeCells"
      :place-random-cells="placeRandomCells"
    />
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
  data(): Data {
    return { columns: 35, rows: 20, board: [] };
  },
  created() {
    this.resetBoard();
  },
  methods: {
    changeCellState(position: Position): void {
      this.board[position.row][position.column].isAlive = !this.board[
        position.row
      ][position.column].isAlive;
      console.log(
        `Now (${position.row},${position.column}) is ${
          this.board[position.row][position.column].isAlive ? "alive" : "dead"
        }`
      );
    },
    resetBoard(): void {
      const board: CellType[][] = [];
      for (let n = 0; n < this.rows; n++) {
        const row: CellType[] = [];
        for (let m = 0; m < this.columns; m++)
          row.push({ isAlive: false, aliveNeighbors: 0 });
        board.push(row);
      }
      this.board = board;
    },
    computeCells(): void {
      const directions: { x: number; y: number }[] = [
        { x: -1, y: -1 },
        { x: 0, y: -1 },
        { x: 1, y: -1 },
        { x: 1, y: 0 },
        { x: 1, y: 1 },
        { x: 0, y: 1 },
        { x: -1, y: 1 },
        { x: -1, y: 0 },
      ];
      for (let n = 0; n < this.rows; n++)
        for (let m = 0; m < this.columns; m++) {
          this.board[n][m].aliveNeighbors = 0;
          for (const direction of directions)
            if (
              n + direction.y >= 0 &&
              n + direction.y < this.rows &&
              m + direction.x >= 0 &&
              m + direction.x < this.columns &&
              this.board[n + direction.y][m + direction.x].isAlive
            ) {
              this.board[n][m].aliveNeighbors++;
            }
        }
      for (let n = 0; n < this.rows; n++)
        for (let m = 0; m < this.columns; m++) {
          if (
            (this.board[n][m].isAlive &&
              (this.board[n][m].aliveNeighbors < 2 ||
                this.board[n][m].aliveNeighbors > 3)) ||
            (!this.board[n][m].isAlive && this.board[n][m].aliveNeighbors === 3)
          )
            this.changeCellState({ row: n, column: m });
        }
    },
    getAliveCells(): number {
      return this.board.flat().filter((cell: CellType) => cell.isAlive).length;
    },
    placeRandomCells(): void {
      const aliveCellsRate = 5;
      const board: CellType[][] = [];
      for (let n = 0; n < this.rows; n++) {
        const row: CellType[] = [];
        for (let m = 0; m < this.columns; m++)
          row.push({
            isAlive:
              Math.floor(Math.random() * Math.floor(aliveCellsRate)) === 0,
            aliveNeighbors: 0,
          });
        board.push(row);
      }
      this.board = board;
    },
  },
});
</script>

<style scoped>
.game-board {
  display: flex;
  flex-flow: column nowrap;
  align-items: center;
  width: 80%;
  margin: auto;
  background-color: rgb(225, 213, 238);
  padding: 2% 5%;
  border-radius: 10px;
}

table {
  border: 1px solid black;
  border-spacing: 0px;
  border-collapse: collapse;
}

td {
  border: 1px solid black;
  width: 30px;
  background-color: white;
  padding: 0;
}

td.alive {
  background-color: rgb(114, 60, 145);
}

td.dead:hover {
  background-color: rgb(183, 164, 189);
}
</style>
