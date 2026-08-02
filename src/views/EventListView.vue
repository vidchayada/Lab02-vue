<script setup lang="ts">
import EventCard from '@/components/EventCard.vue'
import EventInfo from '@/components/EventInfo.vue'
import type { Event } from '@/types'
import { ref, onMounted, computed, watchEffect } from 'vue'
import EventService from '@/services/EventService'
import { useRouter } from 'vue-router'

const router = useRouter()
const events = ref<Event[] | null>(null)
const totalEvents = ref(0)
const hasNextPage = computed(() => {
  const totalPages = Math.ceil(totalEvents.value / 3)
  // const totalPages = Math.ceil(totalEvents.value / perPage.value)
  return page.value < totalPages
})
const props = defineProps({
  page: { type: Number, required: true },
  perPage: { type: Number, required: true },
})
const page = computed(() => props.page)
const perPage = computed(() => props.perPage)

onMounted(() => {
  watchEffect(() => {
    // EventService.getEvents(perPage.value, page.value)
    EventService.getEvents(3, page.value)
      .then((response) => {
        events.value = response.data
        totalEvents.value = response.headers['x-total-count']
      })
      .catch((error) => {
        console.error('There was an error!', error)
      })
  })
})
</script>

<template>
  <h1>Events For Good</h1>

  <div class="flex flex-col items-center">
    <!-- <EventCard v-for="event in events" :key="event.id" :event="event" /> -->
    <div v-for="event in events" :key="event.id" class="event-wrapper">
      <EventCard :event="event" />
      <EventInfo :event="event" />
    </div>
  </div>

  <div class="flex w-[290px]">
    <RouterLink
      id="page-prev"
      class="flex-1 text-left no-underline text-gray-700"
      :to="{ name: 'event-list-view', query: { page: page - 1, perPage: perPage } }"
      rel="prev"
      v-if="page != 1"
      >&#60; Prev Page</RouterLink
    >
    <RouterLink
      id="page-next"
      class="flex-1 text-right no-underline text-gray-700"
      :to="{ name: 'event-list-view', query: { page: page + 1, perPage: perPage } }"
      rel="next"
      v-if="hasNextPage"
      >Next Page &#62;</RouterLink
    >
  </div>
</template>

<!-- <style scoped>
.pagination {
  display: flex;
  width: 290px;
}
.pagination a {
  flex: 1;
  text-decoration: none;
  color: #2c3e50;
}
#page-prev {
  text-align: left;
}
#page-next {
  text-align: right;
}
</style> -->
