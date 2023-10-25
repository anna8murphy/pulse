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
<form @submit.prevent="createGroup(name)">
    <label for="name">Group Name</label>
    <textarea id="name" v-model="name" placeholder="Friends, family, etc." required></textarea>
    <button type="submit" class="pure-button-primary pure-button">Create!</button>
  </form>
</template>


<style scoped>
form {
  background-color: rgba(215, 215, 215, 0.484);
  border-radius: 1em;
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

input[type="checkbox"] {
  margin-left: 0.5em;
}

</style> 


