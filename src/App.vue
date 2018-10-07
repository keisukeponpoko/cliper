<template>
  <div id="app">
    <div id="nav">
      <img src="./assets/logo.png">
    </div>
    <router-view/>
  </div>
</template>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#nav {
  text-align: left;
  img {
    width: 120px;
  }
}
</style>
