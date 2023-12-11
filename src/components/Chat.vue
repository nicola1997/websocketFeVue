<template>
  <main>
    <form class="form-join">
      <input v-model="name" type="text" id="name" maxlength="8" placeholder="Name" size="5" required>
      <input v-model="chatRoom" type="text" id="room" placeholder="Chat room" size="5" required>
      <button @click="sendName" id="join" >Join</button>
    </form>

    <ul class="chat-display"></ul>

    <p class="user-list"></p>

    <p class="room-list"></p>

    <p class="activity">
      {{messaggiChat}}
    </p>

    <form class="form-msg">
      <input v-model="yourMessage" type="text" id="message" placeholder="Your message" required>
      <button type="submit">Send</button>
    </form>
  </main>
</template>

<script setup>
import {ref} from "vue";
let yourMessage=ref('')
let name=ref('')
let chatRoom=ref('')
let messaggiChat=ref('')
let stompClient = new StompJs.Client({
  brokerURL: 'ws://localhost:8080/gs-guide-websocket'
});
stompClient.activate();


stompClient.onConnect = (frame) => {
  stompClient.subscribe('/topic/greetings', (greeting) => {
    showGreeting(JSON.parse(greeting.body).content);
  });
};
function showGreeting(parole){
  messaggiChat.value=parole
}
function sendName() {
  stompClient.publish({
    destination: "/app/hello",
    body: JSON.stringify({'name': name.value})
  });
}
function disconnect() {
  stompClient.deactivate();
  setConnected(false);
  console.log("Disconnected");
}
</script>


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