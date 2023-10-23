<script setup lang="ts">
import { formatDate } from "@/utils/formatDate";
import { fetchy } from "../../utils/fetchy";

const props = defineProps(["group"]);
const emit = defineEmits(["editGroup", "refreshGroups"]);

const deleteGroup = async () => {
  try {
    await fetchy(`api/groups/${props.group.name}`, "DELETE");
    console.log("Done");
  } catch (e) {
    return console.error("Error while deleting group:", e);
  }
  emit("refreshGroups");
};
</script>

<template>
  <h3 class="name">{{ props.group.name }}</h3>
  <div class="base">
    <button class="btn-small pure-button" @click="emit('editGroup', props.group._id)">Edit Name</button>
    <button class="button-error btn-small pure-button" @click="deleteGroup">Delete Group</button>
    <article class="timestamp">
      <p v-if="props.group.dateCreated !== props.group.dateUpdated">Edited on: {{ formatDate(props.group.dateUpdated) }}</p>
      <p v-else>Created on: {{ formatDate(props.group.dateCreated) }}</p>
    </article>
  </div>
</template>

<style scoped>
p {
  margin: 0em;
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

.timestamp {
  display: flex;
  justify-content: flex-end;
  font-size: 0.9em;
  font-style: italic;
}

.base {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.base article:only-child {
  margin-left: auto;
}
</style>
