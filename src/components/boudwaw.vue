<template>
    <div>
      <div v-for="message in messages" :key="message.id">
        <span v-if="message.sender">{{ message.sender }}:</span> {{ message.message }}
      </div>
      <form @submit.prevent="sendMessage">
        <input v-model="message" type="text" placeholder="Type a message...">
        <button type="submit">Send</button>
      </form>
    </div>
  </template>
  
  <script>
  import io from 'socket.io-client';
  
  export default {
    data() {
      return {
        message: '',
        messages: []
      };
    },
    mounted() {
      // Connect to the server
      this.socket = io('http://localhost:3000');
  
      // Listen for messages from the server
      this.socket.on('message', (data) => {
        this.messages.push(data);
      });
    },
    methods: {
      sendMessage() {
        // Send a message to the server
        this.socket.emit('message', {
          message: this.message
        });
  
        // Add the message to the list of messages
        this.messages.push({
          sender: 'You',
          message: this.message
        });
  
        // Clear the input field
        this.message = '';
      }
    }
  };
  </script>
  