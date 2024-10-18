<template>
  <HelloWorld />
</template>

<script>
import HelloWorld from "./components/UserList.vue";
import "@fortawesome/fontawesome-free/css/all.css";

export default {
  name: "App",
  components: {
    HelloWorld,
  },
  setup() {},
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  padding: 2rem;
}

body {
  background-color: #f5f4f4;
}
</style>
