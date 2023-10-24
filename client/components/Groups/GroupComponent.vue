<script setup lang="ts">
import { formatDate } from "@/utils/formatDate";
import { onBeforeMount, ref } from "vue";
import { fetchy } from "../../utils/fetchy";

const props = defineProps(["group"]);
const emit = defineEmits(["editGroup", "refreshGroups"]);
const user = ref("");

let groups = ref<Array<Record<string, string & { members: string[] }>>>([]);
const selectedMembers = ref<Array<string>>([]);
const loaded = ref(false);
let searchGroup = ref("");

async function getGroups(name?: string) {
  let query: Record<string, string> = name !== undefined ? { name } : {};
  let groupResults;
  try {
    groupResults = await fetchy("api/groups", "GET", { query });
  } catch (_) {
    return;
  }
  searchGroup.value = name ? name : "";
  groups.value = groupResults;
}

const emptyForm = () => {
  user.value = "";
};

onBeforeMount(async () => {
  await (getGroups());
  loaded.value = true;
});

const deleteGroup = async () => {
  try {
    await fetchy(`api/groups/${props.group.name}`, "DELETE");
  } catch (e) {
    return console.error("Error while deleting group:", e);
  }
  emit("refreshGroups");
};

async function addMember(group: string, user: string){
  try {
      await fetchy(`api/groups/members/${group}`, "POST", {
      body: {
        member: user,
      },
    });
    } catch (e) {
      return console.error("Error while adding member:", e);
    }
    emptyForm();
    emit("refreshGroups");
};
  
async function deleteSelectedMembers(group: string) {
  const members = selectedMembers.value;
  if (members !== undefined){
    for (const member of members){
    try {
    if (member) {
      await fetchy(`api/groups/members/${group}/${member}`, "DELETE");
    }
    } catch (e) {
      return console.error("Error while deleting member:", e);
    }
  }
  }
  emit("refreshGroups");
}
</script>

<template>
  <h3 class="name">{{ props.group.name }}</h3>
  <div class="base">
    <button class="btn-small pure-button" @click="emit('editGroup', props.group._id)">Edit Name</button>
    <button class="button-error btn-small pure-button" @click="deleteGroup">Delete Group</button>
    <h3> Members </h3>
      <div class="user-list">
      <ul class="member-list">
        <label v-for="member in group.members" :key="member">
          <input type="checkbox" v-model="selectedMembers" :value="member" /> {{ member }}
        </label>
      </ul>
      <button @click="deleteSelectedMembers(group.name)" v-if="group.members.length > 0">Delete Selected</button>
      <form @submit.prevent="addMember(group.name, user)" class="add-member-form">
      <label for="name">Username</label>
      <textarea id="name" v-model="user" placeholder="Username" required></textarea>
      <button type="submit" class="pure-button-primary pure-button">Add Member</button>
  </form>
    </div>
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

.member-list {
  list-style: none;
  padding: 1;
  display: flex;
  flex-wrap: wrap;
  gap: 2px; /* Adjust the gap as needed */
}

.member-list li {
  background-color: var(--base-bg);
  padding: 5px;
  border-radius: 5px;
}

.add-member-form {
  margin-top: 1rem;
}
</style>
