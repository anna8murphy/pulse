<script setup lang="ts">
import { ref } from "vue";
import { fetchy } from "../../utils/fetchy";

const name = ref("");

const emit = defineEmits(["refreshGroups"]);

const createGroup = async (name: string) => {
  try {
    await fetchy("api/groups", "POST", {
      body: {
        name: name,
      },
    });
  } catch (_) {
    return;
  }
  emit("refreshGroups");
  emptyForm();
};

const emptyForm = () => {
  name.value = "";
};

</script>

<template>
<div class="text">
<h2>create a group</h2>
<form @submit.prevent="createGroup(name)">
    <label class="group-name" for="name">group name:</label>
    <textarea id="name" v-model="name" class="input" placeholder="friends, family, etc." required></textarea>
    <button type="submit" class="button pure-button-primary pure-button">Create!</button>
  </form>
</div>
</template>

<style scoped>
form {
  /* border: 1px solid #d8d6d6; */
  border: 1px solid var(--pinkred);
  border-radius: 1em;
  background: #f0efef5a;
  display: flex;
  flex-direction: column;
  gap: 0.5em;
  padding: 1em;
}

textarea {
  font-family: inherit;
  font-size: inherit;
  height: 2em;
  width: 400px;
  padding: 0.5em;
  border-radius: 4px;
  resize: none;
}

.group-name{
  font-size: 1.2em;
}

input[type="checkbox"] {
  margin-left: 0.5em;
}

.button{
  background: var(--pinkred);
  border-radius: 2px;
  margin-left: auto;
  line-height: 1em;
  border-radius: 4px;
  margin-bottom: 0.15em;
  margin-top: .5em;
}

.text {
  font-family: mandali;
}

.input{
  height: 1.5em;
}
</style> 


