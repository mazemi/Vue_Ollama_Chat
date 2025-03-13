# Vue 3 and local LLMA API Chat

This is a simple AI chat application built with Vue.js that interacts with an Ollama-based chatbot API. It allows users to send messages and receive responses from an AI assistant.

## Features

- **User Interaction**: Users can type messages and send them to the chatbot.
- **Assistant Response**: The assistant responds with messages generated based on user input.
- **Message Formatting**: Messages from the assistant are displayed with formatted content (Markdown support).
- **Code Highlighting**: Code responses are highlighted using Prism.js for better readability.

# Ollama Config

The ollama-config.js file contains configuration settings, specifying the API URL and the model used for communication.
see https://github.com/ollama/ollama

## Configuration

```javascript
export const config = {
  apiUrl: "http://127.0.0.1:11434/api/chat", // API endpoint for the chat application
  model: "llama3.2", // Model used for generating responses
};
```

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```


