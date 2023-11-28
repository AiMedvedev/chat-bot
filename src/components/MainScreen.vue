  <template>
    <div id="app">
      <div class="chat-widget">
        <div class="chat-header">Чат с ботом</div>
        <div class="chat-container">
          <div class="chat-messages" ref="messagesContainer">
            <div v-for="(message, index) in messages" :key="index" class="message" :class="{ 'user': message.from === 'user', 'bot': message.from === 'bot' }">
              {{ message.text }}
            </div>
            <div class="chat-buttons" v-if="showButtons">
              <button v-for="topic in buttonTopics" :key="topic" @click="handleUserOptions(topic)">{{ topic }}</button>
            </div>
          </div>
        </div>
        <div class="chat-input">
          <input v-model="userMessage" @keyup.enter="sendMessage" placeholder="Введите сообщение..." />
          <button @click="sendMessage" class="submit-button">Отправить</button>
        </div>
      </div>
    </div>
  </template>

  <script>

  export default {
    data() {
      return {
        showButtons: false,
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
          // Add 10 more topics
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
          // Add responses for other topics
        },
        buttonTopics: [],
        userMessage: '',
      };
    },
    computed: {
      selectedOptions() {                     // Реплики от пользователя
        return this.messages.filter((message) => message.from === 'user').map(item => item["text"]);
      }
    },
    mounted() {
      this.greetUser();
      this.showRandomButtonTopics();
      setTimeout(() => {
        this.showButtons = true;
      }, 1000);
    },
    methods: {
      greetUser() {                           // Приветствия от бота
        const greetingMessage = 'Привет, друг!';
        this.messages.push({ from: 'bot', text: greetingMessage });
        setTimeout(() => {
          this.messages.push({ from: 'bot', text: 'Что я могу для тебя сделать?' });
      }, 1000);
      },
      showRandomButtonTopics() {              // Отображение кнопок с вариантами реплик
        this.buttonTopics = this.getRandomTopics();
      },
      getRandomTopics() {
        const topicsCopy = this.topics.filter(x => !this.selectedOptions.includes(x));
        if (topicsCopy.length < 3) {
          return;
        }
        const randomTopics = [];
        while (randomTopics.length < 3 && topicsCopy.length > 0) {
          const randomIndex = Math.floor(Math.random() * topicsCopy.length);
          const selectedTopic = topicsCopy[randomIndex];
          if (!randomTopics.includes(selectedTopic)) {
            randomTopics.push(selectedTopic);
          }
          topicsCopy.splice(randomIndex, 1); 
        }
        return randomTopics.length === 3 ? randomTopics : [];
      },
      handleUserOptions(userText) {          
        this.messages.push({ from: 'user', text: userText });

        const botResponse = this.responses[userText];

        if (botResponse) {
          const selectedIndex = this.topics.indexOf(userText);

          if (selectedIndex !== -1) {
            this.topics.splice(selectedIndex, 1);

            const newTopics = this.getRandomTopics(3);
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
      scrollToBottom() {                     
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

*, 
*::after, 
*::before {
    box-sizing: inherit;
}

body {
    min-width: 320px;
    min-height: 100vh;
    font-family: Onest, sans-serif;
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
    min-width: 100%;
    height: auto;
}

svg {
    min-height: 100%;
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

h1, h2, h3, p figure {
    margin: 0;
}


#app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.chat-widget {
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

.chat-container {
  flex: 1;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
}

.chat-buttons {
  display: flex;
  justify-content: space-around;
  align-items: center;
  flex-wrap: wrap;
  padding: 15px;
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
}

.message {
  margin-bottom: 15px;
  padding: 12px;
  border-radius: 10px;
}

.message.user {
  background-color: #58a9eb;
  color: white;
  text-align: right;
}

.message.bot {
  background-color: rgb(218, 118, 118);
  color: white;
  text-align: left;
}

.chat-input {
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

.submit-button:hover {
  background-color: #45a049;
}

@media (max-width: 420px) {
  .chat-input {
    flex-direction: column;
    padding: 10px;
  }

  input {
    width: 100%;
    max-width: 300px;
    margin-bottom: 10px;
    margin-left: 15px;
    align-self: center;
    padding: 8px;
}

}
</style>

<!-- 
<style scoped>
#app {
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: Arial, sans-serif;
  height: 100vh;
}

.chat-widget {
  /* max-width: 450px; */
  height: 600px;
  border: 1px solid #ccc;
  border-radius: 10px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

@media (max-width: 600px) {
  .chat-widget {
    height: 100vh;
  }
}

.chat-header {
  background-color: #4CAF50;
  color: white;
  padding: 15px;
  text-align: center;
  font-size: 24px;
  font-weight: 800;
  border-bottom: 2px solid #ccc;
}

.chat-container {
  flex: 1;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
}

.chat-buttons {
  display: flex;
  justify-content: space-around;
  align-items: center;
  padding: 15px;
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
}

.message {
  margin-bottom: 15px;
  padding: 12px;
  border-radius: 10px;
}

.message.user {
  background-color: #2196F3; 
  color: white;
  text-align: right;
}

.message.bot {
  background-color: rgb(218, 118, 118);
  color: white;
  text-align: left;
}

.chat-input {
  padding: 15px;
  background-color: #f2f2f2;
  display: flex;
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
  background-color: #4caf50; /* Green background */
  color: white; /* White text color */
  border: none; /* Remove borders */
  padding: 10px 20px; /* Some padding */
  font-size: 16px; /* Set a font size */
  cursor: pointer; /* Mouse pointer on hover */
  border-radius: 5px; /* Rounded corners */
}

.submit-button:hover {
  background-color: #45a049; /* Dark green background on hover */
}
</style> -->