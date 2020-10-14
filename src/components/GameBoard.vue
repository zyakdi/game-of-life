<template>
  <div class="game-board">
    <table
      @mousedown="onMouseDown"
      @mouseup="onMouseUp"
      @mouseleave="onMouseLeave"
    >
      <tr v-for="(row, rowIndex) in board" :key="rowIndex">
        <td
          v-for="(cell, columnIndex) in row"
          :key="columnIndex"
          :class="{
            alive: cell.isAlive,
            dead: !cell.isAlive,
          }"
        >
          <Cell
            :is-alive="cell.isAlive"
            :position="{ row: rowIndex, column: columnIndex }"
            :is-mouse-down="isMouseDown"
            @change-cell-state="changeCellState"
          />
        </td>
      </tr>
    </table>

    <ControlPanel
      :alive-cells="getAliveCells()"
      :is-playing="isPlaying"
      :min-frequency-ms="MIN_FREQUENCY_MS"
      :max-frequency-ms="MAX_FREQUENCY_MS"
      @pause-game="pauseGame"
      @play-game="playGame"
      @reset-game="resetGame"
      @compute-cells="computeCells"
      @update-speed="updateSpeed"
      @place-random-cells="placeRandomCells"
    />
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import Cell, { Position } from "./Cell.vue";
import ControlPanel from "./ControlPanel.vue";

type CellType = { isAlive: boolean; aliveNeighbors: number };
type Data = {
  columns: number;
  rows: number;
  board: CellType[][];
  isMouseDown: boolean;
  MIN_FREQUENCY_MS?: number;
  MAX_FREQUENCY_MS?: number;
  speedRangeValue: number;
  intervalRef: number | undefined;
  isPlaying: boolean;
};

export default Vue.extend({
  name: "GameBoard",
  components: { Cell, ControlPanel },
  data(): Data {
    return {
      columns: 35,
      rows: 20,
      board: [],
      isMouseDown: false,
      speedRangeValue: 1000,
      intervalRef: undefined,
      isPlaying: false,
    };
  },
  created() {
    this.resetGame();
    this.MIN_FREQUENCY_MS = 50;
    this.MAX_FREQUENCY_MS = 1200;
  },
  beforeDestroy() {
    clearInterval(this.intervalRef);
    this.intervalRef = undefined;
  },
  methods: {
    changeCellState(position: Position): void {
      this.board[position.row][position.column].isAlive = !this.board[
        position.row
      ][position.column].isAlive;
    },
    resetGame(): void {
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
    onMouseDown(): void {
      if (!this.isMouseDown) this.isMouseDown = true;
    },
    onMouseUp(): void {
      if (this.isMouseDown) this.isMouseDown = false;
    },
    onMouseLeave(): void {
      if (this.isMouseDown) this.isMouseDown = false;
    },
    playGame(): void {
      if (this.MIN_FREQUENCY_MS && this.MAX_FREQUENCY_MS) {
        this.intervalRef = setInterval(
          this.computeCells,
          this.MAX_FREQUENCY_MS - this.speedRangeValue + this.MIN_FREQUENCY_MS
        );
        this.isPlaying = true;
      }
    },
    pauseGame(): void {
      clearInterval(this.intervalRef);
      this.intervalRef = undefined;
      this.isPlaying = false;
    },
    updateSpeed(newSpeedValue: number): void {
      this.speedRangeValue = newSpeedValue;
      if (this.isPlaying) {
        this.pauseGame();
        this.playGame();
      }
    },
  },
});
</script>

<style scoped>
.game-board {
  display: flex;
  flex-flow: column nowrap;
  align-items: center;
  width: 1000px;
  margin: auto;
  margin-top: 20px;
  background-color: rgb(225, 213, 238);
  padding: 2% 3%;
  border-radius: 10px;
}

table {
  border: 1px solid rgba(146, 131, 131, 0.664);
  border-spacing: 0px;
  border-collapse: collapse;
}

td {
  border: 1px solid rgba(146, 131, 131, 0.664);
  width: 30px;
  background-color: rgb(248, 246, 239);
  padding: 0;
}

td.alive {
  background-color: rgb(114, 60, 145);
}

td.dead:hover {
  background-color: rgb(183, 164, 189);
}

@media screen and (max-width: 1150px) {
  .game-board {
    width: 90%;
    margin-top: 10px;
  }
}
</style>
