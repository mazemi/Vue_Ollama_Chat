<template>
  <div class="container">
    <Title />
    <Chat />
  </div>
</template>

<script>

import Chat from "./components/Chat.vue";
import Title from "./components/Title.vue";

export default {
  name: "App",
  components: {
    Chat,
    Title
  },
  // data() {
  //   return {
  //   };
  // },
};
</script>

<style>
body {
  font-family: "Roboto", sans-serif;
  font-size: 14px;
}
</style>
