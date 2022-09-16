<template>
  <img alt="logo" src="./assets/logo.png" width="150" />
  <v-select></v-select>
</template>

<script>
import vSelect from "@/components/select";
export default {
  name: "App",
  components: {
    vSelect,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
