<template>
  <div id="app">
    <div class="chat-widget">
      <div class="chat-header">Чат с ботом</div>
      <div class="chat-container">
        <div class="chat-messages">
          <div v-for="(message, index) in messages" :key="index" class="message" :class="{ 'user': message.from === 'user', 'bot': message.from === 'bot' }">
            {{ message.text }}
          </div>
          <div class="chat-buttons" v-if="isBotGreetingComplete && showButtons">
            <button @click="handleUserOptions('Заказать пиццу')">Заказать пиццу</button>
            <button @click="handleUserOptions('Установить будильник')">Установить будильник</button>
            <button @click="handleUserOptions('Вывести погоду')">Вывести погоду</button>
          </div>
        </div>
      </div>
      <div class="chat-input">
        <input v-model="userMessage" @keyup.enter="sendMessage" placeholder="Введите сообщение..." />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isBotGreetingComplete: false,
      showButtons: false,
      messages: [],
      userMessage: '',
    };
  },
  mounted() {
    this.greetUser();
    setTimeout(() => {
      this.showButtons = true;
    }, 1000);
  },
  methods: {
    greetUser() {
      const greetingMessage = 'Привет! Что я могу для Вас сделать?';
      this.messages.push({ from: 'bot', text: greetingMessage });
      this.isBotGreetingComplete = true;
    },
    handleUserOptions(userText) {
      this.messages.push({ from: 'user', text: userText });

      let botResponse = '';

      if (userText.toLowerCase() === 'заказать пиццу') {
        botResponse = 'Хорошо, я закажу пиццу. Что еще могу сделать?';
      } else if (userText.toLowerCase() === 'установить будильник') {
        botResponse = 'Будильник установлен. Чем еще могу помочь?';
      } else if (userText.toLowerCase() === 'вывести погоду') {
        botResponse = 'На улице солнечно и тепло. Что еще интересует?';
      } else {
        botResponse = 'Извините, я не понял ваш запрос. Попробуйте выбрать один из вариантов выше или задайте другой вопрос.';
      }

      this.messages.push({ from: 'bot', text: botResponse });
    },
    sendMessage() {
      if (this.userMessage.trim() !== '') {
        this.messages.push({ from: 'user', text: this.userMessage });
        this.userMessage = '';
      }
    },
  },
};
</script>

<style scoped>
#app {
  display: flex;
  justify-content: center;
  align-items: center;
  max-height: 100%;
  height: 600px;
  margin: 0 auto;
}

.chat-widget {
  width: 450px;
  height: 100%;
  border: 1px solid #ccc;
  border-radius: 5px;
  overflow: hidden;
  font-family: Arial, sans-serif;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.chat-header {
  background-color: #4CAF50;
  color: white;
  padding: 10px;
  text-align: center;
  font-size: 20px;
  font-weight: 800;
}

.chat-container {
  -webkit-box-flex: 1;
  -ms-flex: 1;
  flex: 1;
  overflow-y: auto;
  max-height: 60vh;
  position: relative;
  display: flex;
  flex-direction: column;
}

.chat-buttons {
  display: flex;
  justify-content: space-around;
  align-items: center;
  padding: 10px;
  gap: 8px;
  background-color: #f2f2f2;
}

.chat-buttons button {
  background-color: #4CAF50;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 24px;
  cursor: pointer;
}

.chat-buttons button:hover {
  background-color: #45a049;
}

.chat-messages {
  padding: 10px;
  flex: 1; 
  overflow-y: auto; 
}

.message {
  margin-bottom: 10px;
  padding: 8px;
  border-radius: 5px;
}

.message.user {
  background-color: #2196F3; 
  color: white;
  text-align: right;
}

.message.bot {
  background-color: #f2f2f2;
  color: #333;
  text-align: left;
}

.chat-input {
  padding: 10px;
  background-color: #f2f2f2;
  display: flex;
}

input {
  flex: 1;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-right: 10px;
  align-self: flex-end; 
}
</style>