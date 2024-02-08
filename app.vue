<template>
  <div>
    <p>
      <strong>Server responds:</strong> {{ serverResponse.data }}
      <strong>at</strong>
      {{ serverResponse.at }}
    </p>
    <form @submit.prevent="sendMessage">
      <input v-model="formMessage" />
      <button>Send message</button>
    </form>
  </div>
</template>
<script setup lang="ts">
import io from "socket.io-client"
import type { Socket } from "socket.io-client"

const formMessage = ref("Hello from Nuxt")
const serverResponse = ref({ data: "", at: "" })

let socket: Socket | undefined

const sendMessage = () => {
  socket?.emit("my_custom_event", formMessage.value)
}

onMounted(async () => {
  socket = io("ws://localhost:8000")

  socket.on("my event", (messageValue: { data: ""; at: "" }) => {
    try {
      console.log("Frontend received: ", messageValue)
      serverResponse.value = messageValue
    } catch (e) {
      console.error(e)
    }
  })
})

onUnmounted(() => {
  socket?.disconnect()
})
</script>
