<template>
  <div class="chat-container">
    <div class="chat-box">
      <div v-for="(message, index) in messages" :key="index" class="message">
        <div class="message-role">{{ message.role }}:</div>
        <div
          v-html="formatContent(message.content)"
          class="message-content"
        ></div>
      </div>
    </div>

    <div class="input-box">
      <p v-if="processing" class="processing-container">Processing ...</p>
      <input
        v-model="userInput"
        @keydown.enter="sendMessage"
        placeholder="Type a message..."
      />
      <button @click="sendMessage">Send</button>
    </div>
  </div>
</template>

<script>
import { marked } from "marked";
import Prism from "prismjs";
import "prismjs/themes/prism.css";
import "prismjs/components/prism-javascript.min.js";
import "prismjs/components/prism-python.min.js";
import "prismjs/components/prism-r.min.js";
import { config } from "../ollama-config"; // Import config

export default {
  data() {
    return {
      userInput: "",
      userInputTemp: "",
      messages: [],
      isLoading: false,
      processing: false,
    };
  },
  methods: {
    async sendMessage() {
      if (this.userInput.trim()) {
        this.messages.push({ role: "user", content: this.userInput });

        try {
          this.processing = true;
          this.userInputTemp = this.userInput;
          this.userInput = "";
          this.isLoading = true;

          // Use the values from config.js
          const response = await fetch(config.apiUrl, {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify({
              model: config.model, // Use the model value from config.js
              messages: [
                {
                  role: "user",
                  content: this.userInputTemp,
                },
              ],
              stream: false,
            }),
          });

          const data = await response.json();
          this.processing = false;
          if (data?.message?.content) {
            this.messages.push({
              role: "assistant",
              content: data.message.content,
            });
          } else {
            this.messages.push({
              role: "assistant",
              content: "No content generated.",
            });
          }
        } catch (error) {
          console.error("Error sending message:", error);
        } finally {
          this.isLoading = false;
        }
      }
    },

    formatContent(content) {
      const html = marked(content || "");

      this.$nextTick(() => {
        this.highlightCode();
      });

      return html;
    },

    highlightCode() {
      const codeBlocks = document.querySelectorAll("pre code");
      codeBlocks.forEach((block) => {
        Prism.highlightElement(block);
      });
    },
  },
};
</script>

<style scoped>
html,
body {
  height: 100%;
  margin: 0;
  overflow: hidden;
}

body {
  display: flex;
  flex-direction: column;
}

.chat-container {
  width: 60%;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  background-color: #ffffff;
  border-radius: 8px;
  height: 100%;
  overflow: hidden;
}

.chat-box {
  flex-grow: 1;
  padding: 10px;
  background-color: #fafafa;
  border-radius: 5px;
  overflow-y: auto;
  margin-bottom: 80px;
}

.input-box {
  position: fixed;
  bottom: 0;
  left: 20%;
  width: 60%;
  height: 50px;
  display: flex;
  align-items: center;
  background-color: #f9f9f9;
  padding: 10px;
  box-shadow: 0 -2px 5px rgba(0, 0, 0, 0.1);
  z-index: 100;
}

input {
  flex-grow: 1;
  padding: 8px;
  border-radius: 4px;
  margin-right: 10px;
  border: 1px solid #ccc;
}

button {
  padding: 8px 12px;
  background-color: #3f029b;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #500cb6;
}

button:disabled {
  background-color: #cccccc;
}
.processing-container {
  padding: 10px;
  text-align: center;
  color: rgb(75, 75, 75);
}
.message-role {
  font-weight: bold;
  color: #3f029b;
}

/* a minimal adjsutment for smaller screen */
@media (max-width: 768px) {
  .chat-container {
    width: 95%;
  }

  .input-box {
  left: 2.5%;
  width: 95%;
  align-items: center;
  }
}
</style>
