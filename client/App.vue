<script setup lang="ts">
import { useToastStore } from "@/stores/toast";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";
import { computed, onBeforeMount } from "vue";
import { RouterLink, RouterView, useRoute } from "vue-router";

const currentRoute = useRoute();
const currentRouteName = computed(() => currentRoute.name);
const userStore = useUserStore();
const { isLoggedIn } = storeToRefs(userStore);
const { toast } = storeToRefs(useToastStore());

// Make sure to update the session before mounting the app in case the user is already logged in
onBeforeMount(async () => {
  try {
    await userStore.updateSession();
  } catch {
    // User is not logged in
  }
});
</script>

<template>
  <header>
    <nav v-if="isLoggedIn">
      <div class="title">
        <RouterLink :to="{ name: 'Home' }">
          <h1 class="shimmer-text">pulse</h1>
        </RouterLink>
      </div>
      <ul>
        <li>
          <RouterLink :to="{ name: 'Home' }" :class="{ underline: currentRouteName == 'Home' }"> home </RouterLink>
        </li>
        <li>
          <RouterLink :to="{ name: 'Groups' }" :class="{ underline: currentRouteName == 'Groups' }"> groups </RouterLink>
        </li>
        <li v-if="isLoggedIn">
          <RouterLink :to="{ name: 'Settings' }" :class="{ underline: currentRouteName == 'Settings' }"> settings </RouterLink>
        </li>
        <li v-else>
          <RouterLink :to="{ name: 'Login' }" :class="{ underline: currentRouteName == 'Login' }"> login </RouterLink>
        </li>
      </ul>
    
    <article v-if="toast !== null" class="toast" :class="toast.style">
      <p>{{ toast.message }}</p>
    </article>
  </nav>
  </header>
  <RouterView />
</template>

<style scoped>
@import "./assets/toast.css";

.shimmer-text {
  animation: shimmer 1.5s infinite linear;
  background: linear-gradient(90deg, #dd7d7d, #ebaeae, var(--pinkred));
  background-size: 200% 100%;
  color: transparent;
  background-clip: text;
  -webkit-background-clip: text;
}

@keyframes shimmer {
  to {
    background-position: -200% 0;
  }
}

nav {
  font-family: mandali;
  padding: 1em 2em;
  background-color: rgb(236, 236, 235);
  display: flex;
  align-items: center;
}

h1 {
  font-size: 2em;
  margin: 0;
  color: var(--gray)
  /* color: rgb(93, 103, 91); */
}


.title {
  display: flex;
  align-items: center;
  gap: 0.5em;
}

img {
  height: 2em;
}

a {
  font-size: large;
  color: var(--gray);
  text-decoration: none;
}

ul {
  list-style-type: none;
  margin-left: auto;
  display: flex;
  align-items: center;
  flex-direction: row;
  gap: 1em;
}

.underline {
  text-decoration: underline;
}
</style>
