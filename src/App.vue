<template>
  <div class="bg-gray-100 min-h-screen flex items-center justify-center">
    <div class="max-w-lg w-full mx-4 bg-white shadow-lg rounded-lg">
      <div class="p-4 border-b">
        <h1 class="text-xl font-bold text-gray-800">Vue Chat</h1>
      </div>
      <div class="p-4">
        <div v-if="!username" class="text-center">
          <input type="text" v-model="name" placeholder="Enter your name" class="py-2 px-4 border border-gray-400 rounded-lg w-full mb-4" @keyup.enter="startChat">
          <button class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded-lg w-full" @click="startChat">Start Chatting</button>
        </div>
        <div v-else>
          <div v-for="(message, index) in messages" :key="index" class="mb-2">
            <span v-if="message.sender !== username" class="text-gray-800 font-bold">{{ message.sender }}:</span>
            <span :class="{ 'inline-block align-top mr-0 bg-blue-500 text-white': message.sender === username, 'bg-gray-400': message.sender !== username }" class="py-2 px-4 rounded-lg inline-block">{{ message.message }}</span>
          </div>
          <div class="flex mt-4">
            <input type="text" v-model="message" placeholder="Enter your message" class="py-2 px-4 border border-gray-400 rounded-lg flex-1 mr-2" @keyup.enter="sendMessage">
            <button class="flex bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded-lg" @click="sendMessage">Send</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import io from 'socket.io-client';

export default {
  data() {
    return {
      name: '',
      username: '',
      message: '',
      messages: [],
      socket: null,
    };
  },
  methods: {
    startChat() {
      if (this.name !== '') {
        this.username = this.name;
        this.socket = io('https://socket2.onrender.com');
        this.socket.emit('username', this.username);
        this.socket.on('message', (data) => {
          this.messages.push(data);
        });
      }
    },
    sendMessage() {
      if (this.message !== '') {
        this.socket.emit('message', {
          sender: this.username,
          message: this.message,
        });
        this.messages.push({
          sender: this.username,
          message: this.message,
        });
        this.message = '';
      }
    },
 
  },
};
</script>

