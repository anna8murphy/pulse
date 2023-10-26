<script setup lang="ts">
import { onBeforeMount, ref } from "vue";
import { fetchy } from "../../utils/fetchy";

const props = defineProps(["group"]);
const emit = defineEmits(["editGroup", "refreshGroups"]);
const user = ref("");
const loaded = ref(false);
let groups = ref<Array<Record<string, string & { members: string[] }>>>([]);
let searchGroup = ref("");
const selectedMembers = ref<Array<string>>([]);

const emptyForm = () => {
  user.value = "";
};

onBeforeMount(async () => {
  await (getGroups());
  loaded.value = true;
});

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
  <div class="group">
    <div class="group-header">
      <h2 class="name">{{ props.group.name }}</h2>
      <div class="button-container">
        <button class="edit-button btn-small pure-button" @click="emit('editGroup', props.group._id)">Edit Name</button>
        <button class="delete-button btn-small pure-button" @click="deleteGroup">Delete Group</button>
      </div>
    </div>

    <!-- Members and Add Members form -->
    <div class="members-add-form-container">
      <div class="members-container">
        <h3 class="members-heading">Members</h3>
        <ul class="member-list">
          <label v-for="member in group.members" :key="member">
            <input type="checkbox" v-model="selectedMembers" :value="member" /> {{ member }}
          </label>
        </ul>

        <!-- Delete Members button -->
        <button class="delete-button btn-small pure-button" @click="deleteSelectedMembers(group.name)" v-if="group.members.length > 0">Delete Selected</button>
      </div>

      <!-- Add Members form -->
      <div class="add-member-form">
        <label for="name">Search for a user:</label>
        <textarea class="add-member-input" id="name" v-model="user" placeholder=" Username" required></textarea>
        <button type="submit" class="add-button btn-small pure-button" @click="addMember(group.name, user)">Add Member!</button>
      </div>
    </div>

  </div>
</template>

<style scoped>
.group {
  background-color: #f0f0f0;
  border-radius: 10px;
  padding: 20px;
  margin: 20px 0;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.group-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 10px;
}

.button-container {
  display: flex;
  gap: 10px;
}

.edit-button {
  border-radius: 2px;
  margin-right: auto;
  background-color: #e3e2e2;
  color:  #827f7f;
}

.delete-button {
  border-radius: 2px;
  margin-right: auto;
  color: rgb(249, 249, 249);
  background-color: #ea3b50;
}

.add-button {
  border-radius: 2px;
  margin-right: auto;
  color: #ffffff;
  background-color: var(--base-bg);
}

.members-add-form-container {
  display: flex;
  flex-direction: row; /* Change to row */
  justify-content: space-between;
  align-items: flex-start; /* Align to the top */
  gap: 20px;
}

.members-container {
  flex-direction: column;
  width: 65%; /* Adjust as needed */
}

.members-heading {
  margin-bottom: 5px; /* Add 'px' unit */
}

.member-list {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.add-member-form {
  flex-direction: column;
  width: 30%; 
  margin-top: 30px; 
}

.add-member-input {
  width: 100%; 
  height: 25px;
  border-radius: .5em;
}
</style>





