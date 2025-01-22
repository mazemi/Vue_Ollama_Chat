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
  </div>

  <div class="input-box">
    <div v-if="processing" class="processing-container">
      <h5>Processing ...</h5>
    </div>
    <textarea
      class="text-input"
      v-model="userInput"
      @keydown.enter="sendMessage"
      @keydown="handleKeyDown"
      placeholder="Type a message..."
      autocomplete="off"
    />
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
  props: ['messages'],

  data() {
    return {
      userInput: "",
      userInputTemp: "",
      // messages: [],
      isLoading: false,
      processing: false,
    };
  },
  methods: {
    handleKeyDown(event) {
      if (event.shiftKey && event.key === "Enter") {
        event.preventDefault();
        this.userInput += "\n";
      }
    },

    async sendMessage() {
      if (this.userInput.trim()) {
        // Add the user message to the chat history
        this.messages.push({
          role: "user",
          content: this.userInput,
        });

        try {
          this.processing = true;
          this.userInputTemp = this.userInput;
          this.userInput = "";
          this.isLoading = true;

          // Send the entire message history to the API
          const response = await fetch(config.apiUrl, {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify({
              model: config.model,
              messages: this.messages, // Send full chat history
              stream: false,
            }),
          });

          const data = await response.json();

          this.processing = false;

          if (data?.message?.content) {
            // Add the assistant's response to the chat history
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

    // NewChat() {
    //   this.userInput= "";
    //   this.userInputTemp= "";
    // },

    formatContent(content) {
      const html = marked(content || "", { breaks: true });
      this.$nextTick(() => {
        this.highlightCode();
      });
      return html;
    },
    highlightCode() {
      this.$nextTick(() => {
        const codeBlocks = document.querySelectorAll(
          ".message-content pre code"
        );
        codeBlocks.forEach((block) => {
          Prism.highlightElement(block); // Highlight each code block
        });
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
  padding-top: 30px;
}

.chat-box {
  flex-grow: 1;
  padding: 10px;
  background-color: #fafafa;
  border-radius: 5px;
  overflow-y: auto;
  margin-bottom: 100px;
}

.input-box {
  position: fixed;
  margin: 0 auto;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 60%;
  height: 70px;
  display: flex;
  align-items: center;
  padding: 10px;
  z-index: 100;
}

.processing-container {
  position: absolute;
  z-index: 101;
  top: -40px;
  left: 50%;
  transform: translateX(-50%);
  text-align: center;
  align-items: center;
  width: 120px;
  padding: 0px;
  background-color: rgb(234, 234, 234);
  border-radius: 20px;
}

.processing-container h5{
  margin: 5px;
}

h5 {
  color: #8741f0;
}

.text-input {
  width: 100%;
  height: auto;
  margin: 0 auto;
  padding: 12px;
  border-radius: 12px;
  border: 1px solid #8741f0;
  outline: none;
  resize: vertical;
  font-size: 14px;
  font-family: Arial, sans-serif;
  background-color: #f9f9f9;
  color: #333;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  transition: border-color 0.3s, box-shadow 0.3s;
}

.text-input:focus {
  border-color: #6a2dc9;
  box-shadow: 0 0 5px rgba(138, 43, 226, 0.5);
}

pre code {
  background-color: #f5f5f5;
  padding: 10px;
  border-radius: 5px;
  display: block;
  overflow-x: auto;
  font-family: Consolas, Monaco, "Courier New", Courier, monospace;
  font-size: 14px;
}

.message-role {
  font-weight: bold;
  color: #3f029b;
}

@media (max-width: 600px) {
    .chat-container {
    width: 95%;
  }

  .input-box {
    width: 95%;
  }

}

</style>
