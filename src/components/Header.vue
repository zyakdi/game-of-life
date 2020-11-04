<template>
  <div class="header">
    <div class="github-mirror"></div>
    <div class="title">
      <img class="logo" alt="Molecule icon" src="../assets/molecule-icon.svg" />
      <h1>Conway's Game of Life</h1>
    </div>
    <a
      target="_blank"
      rel="noopener noreferrer"
      class="github"
      href="https://github.com/Dheos-z/game-of-life"
    >
      <img src="../assets/github-icon.svg" alt="Github icon" />
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
  flex-flow: row nowrap;
  justify-content: center;
  align-items: center;
}

.logo {
  width: 100px;
}

.github-mirror {
  width: 50px;
}

.github {
  align-self: flex-end;
  width: 50px;
}

.github > img {
  width: 100%;
}

h1 {
  margin-left: 20px;
  font-size: 30px;
}

@media screen and (max-width: 1150px) {
  .header {
    flex-flow: column nowrap;
    width: 90%;
  }

  .title {
    flex-flow: column nowrap;
  }

  .github {
    order: -1;
  }
}
</style>
