<template>
  <div id="app">
    <Calc />
  </div>
</template>

<script>
import Calc from "./components/Calc.vue";
export default {
  name: "App",
  components: { Calc },
};
</script>

<style>
@import "./assets/styles/default.css";
@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&display=swap");

#app {
  font-family: "Roboto", sans-serif;
  position: absolute;
  left: 50%;
  top: 50%;
  -webkit-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
}
</style>
