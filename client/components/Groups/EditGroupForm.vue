<script setup lang="ts">
import { ref } from "vue";
import { fetchy } from "../../utils/fetchy";
import { formatDate } from "../../utils/formatDate";

const props = defineProps(["group"]);
const changeTo = ref(props.group.name);

const emit = defineEmits(["editGroup", "refreshGroups"]);

const editGroup = async (changeTo: string) => {
  try {
    const groupName = props.group.name;
    await fetchy(`api/groups`, "PATCH", { body: { name: groupName, changeTo: changeTo } });
  } catch (e) {
    return;
  }
  emit("editGroup");
  emit("refreshGroups");
};
</script>

<template>
  <form @submit.prevent="editGroup(changeTo)">
    <textarea id="changeTo" v-model="changeTo" placeholder="Edit group name" required></textarea>
    <div class="base">
      <menu>
        <li><button class="btn btn-small pure-button-primary pure-button" type="submit">Save</button></li>
        <li><button class="btn btn-small pure-button" @click="emit('editGroup')">Cancel</button></li>
      </menu>
      <p v-if="props.group.dateCreated !== props.group.dateUpdated" class="timestamp">Edited on: {{ formatDate(props.group.dateUpdated) }}</p>
      <p v-else class="timestamp">Created on: {{ formatDate(props.group.dateCreated) }}</p>
    </div>
  </form>
</template>

<style scoped>
form {
  background-color: #ffffff;
  display: flex;
  flex-direction: column;
  gap: 0.5em;
}

textarea {
  font-family: inherit;
  font-size: inherit;
  height: 6em;
  border-radius: 4px;
  resize: none;
}

p {
  margin: 0em;
}

.btn{
  background-color: #e0dddd;
  width: 60px;
}
.author {
  font-weight: bold;
  font-size: 1.2em;
}

menu {
  list-style-type: none;
  display: flex;
  flex-direction: row;
  gap: 1em;
  padding: 0;
  margin: 0;
}

.base {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.timestamp {
  display: flex;
  justify-content: flex-end;
  font-size: 0.7em;
  font-style: italic;
}
</style>
