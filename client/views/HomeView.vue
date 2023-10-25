<script setup lang="ts">
import PostListComponent from "@/components/Post/PostListComponent.vue";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";

const { currentUsername, isLoggedIn } = storeToRefs(useUserStore());
</script>

<template>
  <main>
    <!-- <h1>Home Page</h1> -->
    <section>
      <h1 v-if="isLoggedIn">Welcome, {{ currentUsername }}!</h1>
      <h1 v-else>Please login!</h1>
    </section>
    <PostListComponent />
  </main>
</template>

<style scoped>
h1 {
  text-align: center;
  margin-top: 1.5em; /* Remove the default top margin */
  margin-bottom: 1em; /* Add some bottom margin for spacing */
}

</style>
