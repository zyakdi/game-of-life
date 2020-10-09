<template>
  <div class="header">
    <div class="github-mirror"></div>
    <div class="title">
      <img class="logo" alt="Molecule icon" src="../assets/molecule-icon.svg" />
      <h1>Conway's Game of Life</h1>
    </div>
    <a target="_blank" rel="noopener noreferrer" class="github" href="https://github.com/Dheos-z/game-of-life">
      <img src="../assets/github-icon.svg" alt="Github icon">
    </a>
  </div>
</template>

<style scoped>
.header {
  display: flex;
  flex-flow: row nowrap;
  justify-content: space-between;
  width: 1000px;
  margin: auto;
}

.title {
  display: flex;
  flex-flow: row;
  justify-content: center;
  align-items: center;
}

.logo {
  width: 15%;
}

.github-mirror {
  width: 5%;
}

.github {
  align-self: flex-end;
  width: 5%;
}

.github > img {
  width: 100%;
}

h1 {
  margin-left: 3%;
  font-size: 3vw;
}

@media screen and (max-width: 1150px) {
  .header {
    width: 90%;
  }
}
</style>
