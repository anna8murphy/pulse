<script setup lang="ts">
import router from "@/router";
import { useUserStore } from "@/stores/user";
import { ref } from "vue";

const username = ref("");
const password = ref("");
const { createUser, loginUser, updateSession } = useUserStore();

async function register() {
  await createUser(username.value, password.value);
  await loginUser(username.value, password.value);
  void updateSession();
  void router.push({ name: "Home" });
}
</script>

<template>
  <form class="pure-form pure-form-aligned" @submit.prevent="register">
    <h3>register user</h3>
    <fieldset>
      <div class="pure-control-group">
        <label for="aligned-name">username</label>
        <input v-model.trim="username" type="text" id="aligned-name" placeholder="username" required />
      </div>
      <div class="pure-control-group">
        <label for="aligned-password">password</label>
        <input type="password" v-model.trim="password" id="aligned-password" placeholder="password" required />
      </div>
      <div class="pure-controls">
        <button type="submit" class="button pure-button pure-button-primary">register</button>
      </div>
    </fieldset>
  </form>
</template>

<style scoped>
h3 {
  display: flex;
  justify-content: center;
}

.button{
  background: var(--base-bg);
  border-radius: 2px;
  margin-left: auto;
  line-height: 1em;
  border-radius: 4px;
  margin-bottom: 0.15em;
  margin-top: .5em;
}
</style>
