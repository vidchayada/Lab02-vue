<script setup lang="ts">
import { toRefs } from 'vue'
import { type Event } from '@/types'
import { useRouter } from 'vue-router'
import { useMessageStore } from '@/stores/message'

const router = useRouter()
const store = useMessageStore()

const props = defineProps<{
  event: Event
}>()
// eslint-disable-next-line @typescript-eslint/no-unused-vars
const { event } = toRefs(props)
const editEvent = () => {
  // เรียก API แก้ไขข้อมูล event ตรงนี้ (ถ้ามี)
  store.updateMessage('The event has been updated!')
  setTimeout(() => {
    store.resetMessage()
  }, 3000)
  router.push({ name: 'event-detail-view', params: { id: props.event.id } })
}
</script>

<template>
  <button @click="editEvent">Edit</button>
</template>
