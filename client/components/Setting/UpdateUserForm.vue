<script setup lang="ts">
import { useUserStore } from "@/stores/user";
import { ref } from "vue";

let username = ref("");
let password = ref("");

const { updateUser, updateSession } = useUserStore();

async function updateUsername() {
  await updateUser({ username: username.value });
  await updateSession();
  username.value = "";
}

async function updatePassword() {
  await updateUser({ password: password.value });
  await updateSession();
  password.value = "";
}
</script>

<template>
  <div class="text">
    <h2>update user details</h2>
  <form @submit.prevent="updateUsername" class="pure-form">
    <fieldset>
      <legend>change your username</legend>
      <input type="text" class="input-spacing" placeholder="new username" v-model="username" required />
      <button type="submit" class=" btn pure-button pure-button-primary">Update username</button>
    </fieldset>
  </form>

  <form @submit.prevent="updatePassword" class="pure-form">
    <fieldset>
      <legend>change your password</legend>
      <input type="password" class="input-spacing" placeholder="new password" v-model="password" required />
      <button type="submit" class="btn pure-button pure-button-primary">Update password</button>
    </fieldset>
  </form>
  </div>
  
</template>

<style>

.text {
  font-family: mandali;
}

.btn {
  padding: 5px;
  height: 2.75em;
  width: 10em;
  font-size: 15px;
  background: var(--base-bg);
  color: white;
  border-radius: .25em;
}

.input-spacing {
  margin-right: 5px;
}

</style>