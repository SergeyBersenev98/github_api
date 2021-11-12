<template>
  <Searcher />
</template>

<script>
import Searcher from "./components/Searcher.vue";

//https://api.github.com/search/users?q=Sergey

export default {
  name: "App",
  components: {
    Searcher,
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color: #edf7ef;
  min-height: 100vh;
}
</style>
