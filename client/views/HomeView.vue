<script setup lang="ts">
import PostListComponent from "@/components/Post/PostListComponent.vue";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";

const { currentUsername, isLoggedIn } = storeToRefs(useUserStore());

const currentTime = new Date();
const currentHour = currentTime.getHours();

const isMorning = currentHour >= 5 && currentHour < 12;
const isEvening = currentHour >= 18 || currentHour < 5;

</script>

<template>
  <main>
    <!-- <h1>Home Page</h1> -->
    <section>
      <h1 v-if="isLoggedIn">{{ isMorning ? 'good morning' : (isEvening ? 'good evening' : 'Hello') }}, {{ currentUsername }}!</h1>
      <h1 v-else>Please login!</h1>
    </section>
    <PostListComponent />
  </main>
</template>

<style scoped>
h1 {
  font-family: mandali ;
  text-align: center;
  margin-top: 1.5em; /* Remove the default top margin */
  margin-bottom: 1em; /* Add some bottom margin for spacing */
}


</style>
