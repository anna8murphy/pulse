<script setup lang="ts">
import GroupListComponent from "@/components/Groups/GroupListComponent.vue";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";

const { currentUsername, isLoggedIn } = storeToRefs(useUserStore());
</script>

<template>
  <main>
    <h1>groups</h1>
    <section>
      <h1 v-if="isLoggedIn"></h1>
      <h1 v-else>Please login!</h1>
      <GroupListComponent/>
    </section>
    </main>
</template>

<style scoped>
h1 {
  color: var(--gray);
  font-family: mandali;
  text-align: center;
  margin-bottom: 2em;
  margin-top: 1em;
}

.shimmer-text{
  animation: shimmer 3s infinite linear;
  filter: grayscale(100%);
}

</style>
