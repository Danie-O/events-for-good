<script setup>
  import { ref, onMounted, watchEffect, computed } from 'vue'
  import EventCard from '../components/EventCard.vue'
  import EventService from '../services/EventService.js'

  const events = ref(null)
  const totalEvents = ref(0)

  const props = defineProps({
    page: {
      required: true
    }
  })

  onMounted(() => {
    watchEffect(() => {
      events.value = null
      EventService.getEvents(2, props.page)
      .then((response) => {
        events.value = response.data
        totalEvents.value = response.headers['x-total-count']
        hasNextPage
      })
      .catch((error) => {
        console.log(error) 
      })
    })
  })

  const hasNextPage = computed(() => {
    const totalPages = Math.ceil(totalEvents.value / 2)
    return props.page < totalPages
  })
</script>

<template>
  <h2 class="heading-text">Events for good </h2>
  <div class="events">
     <EventCard v-for="event in events" :key="event.id" :event="event"/>
     <div class="pagination">
      <router-link
        id="page-prev"
        :to="{ name: 'event-list', query: { page: page - 1 } }"
        rel="prev"
        v-if="page != 1"
        >&#60; Previous</router-link
      >

      <router-link
        id="page-next"
        :to="{ name: 'event-list', query: { page: page + 1 } }"
        rel="next"
        v-if="hasNextPage"
        >Next &#62;</router-link
      >
    </div>
  </div>
</template>

<style scoped>
.heading-text {
  padding-top: 10px;
  text-align: center;
  color: #42b983;
}
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}

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
</style>
