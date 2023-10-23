<script setup lang="ts">
import GroupListComponent from "@/components/Groups/GroupListComponent.vue";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";

const { currentUsername, isLoggedIn } = storeToRefs(useUserStore());
</script>

<template>
  <main>
    <h1>Groups</h1>
    <section>
      <h1 v-if="isLoggedIn">Manage your Groups:</h1>
      <h1 v-else>Please login!</h1>
      <GroupListComponent/>
    </section>
    </main>
</template>

<style scoped>
h1 {
  text-align: center;
}
</style>
