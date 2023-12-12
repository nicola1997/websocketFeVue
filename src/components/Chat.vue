<template>
  <main>
    <form class="form-join">
      <input v-model="name" type="text" id="name" maxlength="8" placeholder="Name" size="5" required>
      <input v-model="chatRoom" type="text" id="room" placeholder="Chat room" size="5" required>
      <button type="button" @click="sendMessage" id="join">Join</button>
    </form>

    <ul class="chat-display">
      <li style="color: crimson" v-for="m in messaggi" :key="m" class="post">
        {{ m }}
      </li>
    </ul>

    <form class="form-msg">
      <input :disabled="isDisabled" v-model="yourMessage" type="text" id="message" placeholder="Your message" required>
      <button :disabled="isDisabled" @click=sendMessage type="button">Send</button>
    </form>
  </main>
</template>

<script setup>
import { ref } from "vue";
let yourMessage = ref('');
let name = ref('');
let chatRoom = ref('');
let messaggi = ref([]);
let stompClient = new StompJs.Client({
  brokerURL: 'wss://websocketbe1-f140b52c1254.herokuapp.com/gs-guide-websocket'
});

stompClient.activate();
let isDisabled=ref(true)


stompClient.onConnect = () => {
  stompClient.subscribe('/topic/greetings', (message) => {
    const dataCorrente = new Date();
    const oraMinuti = dataCorrente.toLocaleTimeString('it-IT', { hour: '2-digit', minute: '2-digit' });
    messaggi.value.push(JSON.parse(message.body).name+": "+JSON.parse(message.body).messaggio+" " + oraMinuti);
  });
};
function sendMessage() {
  const nameTemp=name.value
  const yourMessageTemp=yourMessage.value
  name.value=" "
  yourMessage.value=" "
  isDisabled.value=false
  stompClient.publish({
    destination: "/app/message",
    body: JSON.stringify({
      'name': nameTemp.value,
      'messaggio': yourMessageTemp.value
    })
  });
}

function disconnect() {
  stompClient.deactivate();
  setConnected(false);
  console.log("Disconnected");
}
</script>

<style scoped>
/* CSS styles */
</style>


<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-size: 1rem;
  font-family: Arial, Helvetica, sans-serif;
}
body {
  background-color: #000;
  color: #fff;
}

main {
  height: 100vh;
  padding: 1rem;
  display: flex;
  flex-flow: column;
}

form {
  width: 100%;
  margin: auto;
  max-width: 600px;
  display: flex;
  flex-flow: row nowrap;
  justify-content: center;
  gap: .25rem;
}

input {
  flex-grow: 1;
  max-width: calc(80% - .25rem);
}

button {
  width: 20%;
}

input,
button {
  border-radius: 10px;
  padding: .5rem;
}

.chat-display {
  background-color: #333;
  list-style-type: none;
  width: 100%;
  max-width: 600px;
  border-radius: 10px;
  margin: 1rem auto;
  padding: 0;
  display: flex;
  flex-flow: column;
  justify-content: left;
  overflow: auto;
  flex-grow: 1;
}

.post {
  background-color: #eee;
  border-radius: 10px;
  padding: 0 0 .25rem;
  margin: .5rem;
  overflow: hidden;
  flex-shrink: 0;
}

.post--left {
  width: 60%;
  align-self: flex-start;
}

.post--right {
  width: 60%;
  align-self: flex-end;
}

.post__header {
  color: #fff;
  padding: .25rem .5rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: .5rem;
}

.post__header--user {
  background-color: blue;
}

.post__header--reply {
  background-color: purple;
}

.post__header--name {
  font-weight: bold;
}

.post__header--time {
  font-size: .8rem;
}

.post__text {
  margin-top: 5px;
  color: #333;
  padding: .25rem .5rem;
}

.user-list,
.room-list,
.activity {
  width: 100%;
  min-height: 2.65rem;
  margin: 0 auto;
  max-width: 600px;
  padding: .75rem .25rem;
}

.activity {
  font-style: italic;
}
</style>
