<script setup lang="ts">
import { ref } from "vue";

const author = ref("");
const emit = defineEmits(["getPostsByAuthor"]);
</script>

<template>
  <form @submit.prevent="emit('getPostsByAuthor', author)" class="pure-form">
    <fieldset>
      <legend class="text">search by user</legend>
      <div class="input-container">
        <input id="author" type="text" v-model="author" class="text" placeholder="username" />
        <button type="submit" class="text search-button pure-button pure-button-primary">search</button>
      </div>
    </fieldset>
  </form>
</template>

<style scoped>
form {
  display: flex;
  gap: 0.5em;
  padding: 1em;
  align-items: center;
}

.text {
  font-family: mandali;
}

.input-container {
  display: flex; 
  align-items: center;
  gap: 0.5em;
}

.search-button {
  background-color: var(--base-bg);
  height: 2em;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>

