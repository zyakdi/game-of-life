<template>
  <div class="home">
    <Header />
    <GameBoard />
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import GameBoard from "../components/GameBoard.vue";
import Header from "../components/Header.vue";

export default Vue.extend({
  name: "Home",
  components: {
    GameBoard,
    Header
  }
});
</script>
