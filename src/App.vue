<script setup>
  import { io } from 'socket.io-client'
  import { onBeforeMount, ref} from "vue"

  const socket = io('http://localhost:3000')
  const messages = ref([])
  const texts = ref('')
  const joined = ref(false)
  const name = ref('')

  onBeforeMount(() => {
    socket.emit('findAllMessages', {}, (response) =>{
      messages.value = response
    })

    socket.on('message', (msg)=>{
      messages.value.push(msg)
    })
  })

  const join = ()=> {
    socket.emit('join', {name: name.value}, () =>{
      joined.value = true
    })
  }

  const sendMsg = () => {
    socket.emit('createMessage', { text : texts.value}, ()=> {
      texts.value = ''
    })
  }
</script>

<template>
  <div class="chat">
    <div v-if="!joined">
      <form @submit.prevent="join">
        <label>
          Please enter your name:
        </label>
        <input v-model="name" />
        <button type="submit">Submit</button>
      </form>
      
    </div>
    <div class="chat-container" v-else>
      <div class="messages-container">
        <div v-for="message in messages">
        [{{ message.name }}]: {{ message.text }}
        </div>
      </div>

      <div class="message-input">
        <form @submit.prevent="sendMsg">
          <label>Message:</label>
          <input v-model="texts">
          <button type="submit">Send</button>
        </form>
      </div>
    </div>
  </div>
</template>

<style>

.chat {
  padding: 20px;
  height: 100vh;
}

.chat-container{
  display: flex;
  flex-direction: column;
  height: 100%;
}

.messages-container{
  flex: 1;
}


</style>
