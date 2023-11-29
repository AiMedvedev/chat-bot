<template>
  <div id="app">
    <section class="chat-widget" v-if="chatIsShown">
      <div class="close-button" @click="closeChat">✖</div>
      <h1 class="chat-header">Чат с ботом</h1>
      <div class="chat-container">
        <div class="chat-messages" ref="messagesContainer">
          <button v-for="(message, index) in messages" :key="index" class="message" :class="{ 'user': message.from === 'user', 'bot': message.from === 'bot' }">
            {{ message.text }}
          </button>
          <div class="chat-options" v-if="showButtons">
            <h3 class="chat-options_title" v-if="buttonTopics.length > 2">Варианты реплик</h3>
            <h3 class="chat-options_title" v-else>Варианты реплик закончились</h3>
            <div class="chat-buttons">
              <button v-for="topic in buttonTopics" :key="topic" @click="handleUserOptions(topic)">{{ topic }}</button>
            </div>
          </div>
        </div>
      </div>
      <div class="chat-input">
        <input v-model="userMessage" @keyup.enter="sendMessage" placeholder="Введите сообщение..." />
        <button @click="sendMessage" class="submit-button">Отправить</button>
      </div>
    </section>
    <div v-else class="chat-widget chat-widget_hidden">
      <button @click="openChat" class="submit-button open-chat-button">Открой меня</button>
    </div>
  </div>
</template>

<script>

export default {
  data() {
    return {
      showButtons: false,
      greetingsDone: false,
      messages: [],
      topics: [
        'Заказать пиццу',
        'Установить будильник',
        'Вывести погоду',
        'Как дела?',
        'Тема 5',
        'Тема 6',
        'Тема 7',
        'Тема 8',
        'Тема 9',
        'Тема 10',
        'Тема 11',
        'Тема 12',
        'Тема 13',
        'Тема 14',
      ],
      responses: {
        'Заказать пиццу': 'Хорошо, я закажу пиццу. Что еще могу сделать?',
        'Установить будильник': 'Будильник установлен. Чем еще могу помочь?',
        'Вывести погоду': 'На улице солнечно и тепло. Что еще интересует?',
        'Как дела?': 'Всё отлично. Расскажи свои новости.',
        'Тема 5': 'Ответ на тему 5',
        'Тема 6': 'Ответ на тему 6',
        'Тема 7': 'Ответ на тему 7',
        'Тема 8': 'Ответ на тему 8',
        'Тема 9': 'Ответ на тему 9',
        'Тема 10': 'Ответ на тему 10',
        'Тема 11': 'Ответ на тему 11',
        'Тема 12': 'Ответ на тему 12',
        'Тема 13': 'Ответ на тему 13',
        'Тема 14': 'Ответ на тему 14',
      },
      buttonTopics: [],
      userMessage: '',
      chatIsShown: false,
      topicsNumber: 3
    };
  },
  computed: {
    selectedOptions() {                     // Вырианты реплик, уже выбранные пользователем
      return this.messages.filter((message) => message.from === 'user').map(item => item["text"]);
    }
  },
  methods: {
    openChat() {
      this.chatIsShown = true;

      if(!this.greetingsDone) {
        this.greetingsDone = true;
        this.greetUser();
      }
      this.showRandomButtonTopics();
      setTimeout(() => {
        this.showButtons = true;
      }, 1000);
    },
    closeChat() {
      this.chatIsShown = false;
    },
    greetUser() {                          
      const greetingMessage = 'Привет, друг!';
      this.messages.push({ from: 'bot', text: greetingMessage });
      setTimeout(() => {
        this.messages.push({ from: 'bot', text: 'Что я могу для тебя сделать?' });
    }, 1000);
    },
    showRandomButtonTopics() {              
      this.buttonTopics = this.getRandomTopics();
    },
    getRandomTopics() {    // Получение трех вариантов запросов из массива, для отображения в кнопках
      const topicsCopy = this.topics.filter(x => !this.selectedOptions.includes(x));
      
      if (topicsCopy.length < this.topicsNumber) {
        return;
      }
      const randomTopics = [];
      while (randomTopics.length < this.topicsNumber && topicsCopy.length > 0) {
        const randomIndex = Math.floor(Math.random() * topicsCopy.length);
        const selectedTopic = topicsCopy[randomIndex];
        if (!randomTopics.includes(selectedTopic)) {
          randomTopics.push(selectedTopic);
        }
        topicsCopy.splice(randomIndex, 1); 
      }
      return randomTopics.length === this.topicsNumber ? randomTopics : [];
    },
    handleUserOptions(userText) {    // Сверка введенного запроса с возможностью ответа от бота и вывод сообщений (запрос от пользователя и ответ бота) на экран
      this.messages.push({ from: 'user', text: userText });

      const botResponse = this.responses[userText];
      
      if (botResponse) {
        const selectedIndex = this.topics.indexOf(userText);

        if (selectedIndex !== -1) {
          this.topics.splice(selectedIndex, 1);

          const newTopics = this.getRandomTopics(this.topicsNumber);
          this.topics = [...this.topics, ...newTopics];

          this.messages.push({ from: 'bot', text: botResponse });
          this.scrollToBottom();
          this.showRandomButtonTopics();
        }
      } else {
        const defaultBotResponse = 'Извините, я не понял ваш запрос. Попробуйте выбрать один из вариантов ниже или задайте другой вопрос.';
        this.messages.push({ from: 'bot', text: defaultBotResponse });
        this.scrollToBottom();
        this.showRandomButtonTopics();
      }
    },
    sendMessage() {                        
      if (this.userMessage.trim() !== '') {
        this.handleUserOptions(this.userMessage);
        this.userMessage = '';
      }
    },
    scrollToBottom() {      // Установка скролла в нижнем положении, чтобы всегда было видно последнее сообщение               
      this.$nextTick(() => {
        const messagesContainer = this.$refs.messagesContainer;
        messagesContainer.scrollTop = messagesContainer.scrollHeight;
      });
    },
  },
};
</script>


<style scoped>
@import url('https://fonts.googleapis.com/css?family=Lato:400,700');

html {
  box-sizing: border-box;
}

*, *::after, *::before {
  box-sizing: inherit;
}

body {
  min-width: 300px;
  min-height: 100vh;
  font-family: 'Lato', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-rendering: optimizeLegibility;
  -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
  -webkit-tap-highlight-color: transparent;
  font-size: 18px;
  line-height: 1.4;
  color: #1A1A1A;
  background-color: #fff;
}

img, svg {
  display: block;
  max-width: 100%;
  height: auto;
}

button {
  padding: 0;
  cursor: pointer;
  border: none;
  background-color: transparent;
  color: inherit;
}

a {
  text-decoration: none;
  color: inherit;
}

ul {
  padding: 0;
  margin: 0;
  list-style: none;
}

h1, h2, h3, p, figure {
  margin: 0;
}

#app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.chat-widget {
  position: relative;
  width: 100%;
  max-width: 400px;
  height: 80vh;
  border: 1px solid #ccc;
  border-radius: 10px;
  overflow: hidden;
  font-family: 'Lato', sans-serif;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.1);
  transition: max-height 0.8s ease-in-out;
}

.chat-header {
  background-color: #4CAF50;
  color: white;
  padding: 15px;
  text-align: center;
  font-size: 24px;
  font-weight: 800;
  border-bottom: 2px solid #ccc;
  border-radius: 10px 10px 0 0;
}

.closed {
  max-height: 0;
  overflow: hidden;
}

.close-button {
  position: absolute;
  top: 10px;
  right: 15px;
  cursor: pointer;
  font-size: 20px;
  transition: color .5s;
  color: rgb(6, 65, 104);
}

.close-button:hover {
  color: red;
}

.chat-container {
  flex: 1;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
}

.chat-options {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin-top: 8px;
}

.chat-options_title {
  color: #58a9eb;
}
.chat-buttons {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  padding: 8px;
  gap: 8px;
}

.chat-buttons button {
  background-color: #4CAF50;
  color: white;
  border: none;
  padding: 8px 12px;
  border-radius: 22px;
  cursor: pointer;
  font-size: 16px;
}

.chat-buttons button:hover {
  background-color: #45a049;
}

.chat-messages {
  padding: 15px;
  flex: 1;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
}

.message {
  font-size: 16px;
  margin-bottom: 15px;
  padding: 8px 12px;
  border-radius: 15px;
}

.message.user {
  background-color: #58a9eb;
  color: white;
  align-self: self-end;
  text-align: right;
  width: fit-content;
}

.message.bot {
  background-color: rgb(218, 118, 118);
  color: white;
  width: fit-content;
  text-align: left;
}

.chat-input {
  position: relative;
  padding: 15px;
  background-color: #f2f2f2;
  display: flex;
  border-radius: 0 0 10px 10px;
}

input {
  flex: 1;
  padding: 12px;
  border: 2px solid #ccc;
  border-radius: 10px;
  margin-right: 15px;
  align-self: flex-end;
  font-size: 16px;
}

.submit-button {
  background-color: #4caf50;
  color: white;
  border: none;
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
  border-radius: 5px;
}

.chat-widget_hidden {
  border: none;
  box-shadow: none;
}

.open-chat-button {
  position: absolute;
  height: 26px;
  bottom: 15px;
  right: 15px;
}

.submit-button:hover {
  background-color: #45a049;
}

@media (max-width: 420px) {
  .chat-input {
    flex-direction: column;
    padding: 10px;
  }

  input {
    width: calc(100% - 15px);
    margin-bottom: 10px;
    margin-left: 15px;
    align-self: center;
    padding: 8px;
  }
}
</style>