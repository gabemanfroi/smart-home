<template>
  <div id="app">
    <MoistureSensor />
  </div>
</template>

<script>
import MoistureSensor from "./components/MoistureSensor.vue";

export default {
  components: {
    MoistureSensor
  }
};
</script>

<style>
#app {
  max-width: 600px;
  margin: auto;
  padding: 20px;
}
</style>