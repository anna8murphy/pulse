<script setup lang="ts">
import router from "@/router";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";
import UpdateUserForm from "../components/Setting/UpdateUserForm.vue";

const { currentUsername } = storeToRefs(useUserStore());
const { logoutUser, deleteUser } = useUserStore();

async function logout() {
  await logoutUser();
  void router.push({ name: "Home" });
}

async function delete_() {
  await deleteUser();
  void router.push({ name: "Home" });
}
</script>

<template>
  <div class="text">
    <main class="column">
    <h1>settings for {{ currentUsername }}</h1>
    <button class="logout-btn pure-button pure-button" @click="logout">Logout</button>
    <button class="delete-btn pure-button" @click="delete_">Delete User</button>
    <UpdateUserForm />
  </main>
  </div>
  
</template>

<style>

.text {
  color: var(--gray);
  font-family: mandali;
}

.logout-btn {
  display: flex;
  align-content: center;
  justify-content: center;
  text-align: center;
  height: 3em;
  width: 7em;
  font-size: 15px;
  background: var(--base-bg);
  color: white;
  border-radius: .25em;
}

.delete-btn {
  display: flex;
  align-content: center;
  justify-content: center;
  text-align: center;
  height: 3em;
  width: 7em;
  font-size: 15px;
  background: var(--pinkred);
  color: white;
  border-radius: .25em;
}

</style>