<template>
  <div class="container">
    <Nav @StratNewChat="ClearChat"/>
    <Chat :messages="messages"/>
  </div>
</template>

<script>

import Chat from "./components/Chat.vue";
import Nav from "./components/Nav.vue";

export default {
  name: "App",
  components: {
    Nav,
    Chat,
  },
  data() {
    return {
      messages: [],
    };
  },

  methods: {
    ClearChat() {
      this.messages = [];
    },
  },
};
</script>

<style>
body {
  font-family: "Roboto", sans-serif;
  font-size: 14px;
}
</style>
