<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { type Event } from '@/types'
import EventService from '@/services/EventService'
import { useMessageStore } from '@/stores/message'
import { storeToRefs } from 'pinia'

const store = useMessageStore()
const { message } = storeToRefs(store)
const event = ref<Event | null>(null)
const props = defineProps({
  id: {
    type: String,
    required: true,
  },
})

onMounted(() => {
  EventService.getEvent(Number(props.id))
    .then((response) => {
      event.value = response.data
    })
    .catch((error) => {
      console.error('There was an error!', error)
    })
})
</script>

<template>
  <div id="flashMessage" v-if="message">
    <h4>{{ message }}</h4>
  </div>
  <div v-if="event">
    <h1>{{ event.title }}</h1>
    <p>{{ event.time }} on {{ event.date }} @ {{ event.location }}</p>
    <p>{{ event.description }}</p>
  </div>
</template>

<style scoped>
@keyframes yellowFade {
  from {
    background-color: yellow;
  }
  to {
    background-color: transparent;
  }
}
#flashMessage {
  animation: yellowFade 3s ease-in-out;
}
</style>
