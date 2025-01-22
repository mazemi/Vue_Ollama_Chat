<template>
  <nav class="navbar">
    <div class="icon-container" @click="NewChat" title="Start New Chat">
      <i class="fas fa-pen-to-square new-chat-icon"></i>
    </div>
    <div class="title-container">
      <h1 class="title">Interactive AI Chat Application with Vue.js and local Ollama</h1>
    </div>
  </nav>
</template>

<script>
export default {
  name: "App",
  methods: {
    NewChat() {
      this.$emit('StratNewChat');
      console.log("New Chat Started");
    },
  },
};
</script>

<style scoped>

.navbar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background-color: #f9f9f9;
  padding: 10px 15px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 1000;
}

.icon-container {
  display: flex;
  align-items: center;
  cursor: pointer;
}

.new-chat-icon {
  font-size: .9rem; 
  color: #4801b3;
  transition: color 0.3s ease, transform 0.3s ease;
}

.new-chat-icon:hover {
  color: rgb(127, 7, 201);
}

.title-container {
  flex-grow: 1;
  text-align: center;
}

.title {
  font-size: .8rem;
  color: #4801b3;
  margin: 0;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}


@media (max-width: 600px) {
  .title {
    font-size: 0.7rem; 
  }

  .new-chat-icon {
    font-size: .8rem; 
  }

  .navbar {
    padding: 8px 10px; 
  }
}
</style>
