<script setup lang="ts">
import PostListComponent from "@/components/Post/PostListComponent.vue";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";
import LoginView from "./LoginView.vue";

const { currentUsername, isLoggedIn } = storeToRefs(useUserStore());

const currentTime = new Date();
const currentHour = currentTime.getHours();

const isMorning = currentHour >= 5 && currentHour < 12;
const isEvening = currentHour >= 18 || currentHour < 5;
</script>

<template>
  <main>
    <section>
      <h1 v-if="isLoggedIn">
        <span>
          {{ isMorning ? 'good morning' : (isEvening ? 'good evening' : 'good afternoon') }},
        {{ currentUsername }}! </span>
      </h1>
      <template v-if="isLoggedIn">
        <PostListComponent />
      </template>
      <LoginView v-else/>
    </section>
  </main>
</template>

<style scoped>
h1 {
  font-family: mandali;
  color: var(--gray);
  text-align: center;
  margin: 0;
  margin-top: 1.5em;
  margin-bottom: 1em;
}

</style>

