<template>
  <div class="app">
    <Form />
  </div>
</template>

<script>
import { Form } from "./components";

export default {
  name: "App",
  components: {
    Form,
  },
};
</script>

<style scoped>
.app {
  background-image: url(./assets/background2.jpg);
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
  width: 100vw;
  height: 100vh;
  padding: 10px;
  display: grid;
  place-items: center;
  overflow: hidden;
}
</style>
