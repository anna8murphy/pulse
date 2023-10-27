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
const isDropdownOpen = ref(false);

const toggleDropdown = () => {
  isDropdownOpen.value = !isDropdownOpen.value;
};

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
  <div class="text">
  <div class="group">
    <div class="group-header">
      <div class="header-left">
          <h2 class="group-name">{{ props.group.name }}</h2>
        </div>
        <div class="dropdown-container">
          
          <div v-if="isDropdownOpen" class="dropdown">
            <ul>
              <button class="edit-button btn-small pure-button" @click="emit('editGroup', props.group._id)">Edit Name</button>
              <button class="delete-button btn-small pure-button" @click="deleteGroup">Delete Group</button>
            </ul>
          </div>
          <a class="svg" @click="toggleDropdown">
            <svg xmlns="http://www.w3.org/2000/svg" height="1.25em" viewBox="0 0 128 512">
              <path d="M64 360a56 56 0 1 0 0 112 56 56 0 1 0 0-112zm0-160a56 56 0 1 0 0 112 56 56 0 1 0 0-112zM120 96A56 56 0 1 0 8 96a56 56 0 1 0 112 0z" />
            </svg>
          </a>
        </div>
      </div>

    <!-- Members and Add Members form -->
    <div class="members-add-form-container">

       <!-- Add Members form -->
       <div class="add-member-form">
        <label for="name"></label>
        <textarea class="add-member-input" id="name" v-model="user" placeholder=" username" required></textarea>
        <button type="submit" class="add-button btn-small pure-button" @click="addMember(group.name, user)">Add Member!</button>
      </div>
      
      <div class="members-container">
        <h3 class="members-heading">members</h3>
        <ul class="member-list">
        <label v-for="member in group.members" :key="member">
          <input type="checkbox" v-model="selectedMembers" :value="member" /> {{ member }}
        </label>
      </ul>
    </div>
      <button class="delete-button btn-small pure-button" @click="deleteSelectedMembers(group.name)" v-if="group.members.length > 0">Delete Selected</button>
    </div>
  </div>
</div>
</template>

<style scoped>
.svg {
  /* position: absolute; */
  margin-bottom: 8px; /* Adjust the top position as needed */
}

.svg:hover {
    cursor: pointer;
  }

a.svg {
 position: relative;
 display: inline-block;
 width: 25%;
}
a.svg:after {
  content: ""; 
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left:0;
}
object {
  width: 100%;
}

ul {
  margin: 16px;
  padding: 0px;
}

.dropdown-container {
  border-radius: .5em;
  display: flex;
  align-items: center;
  }

.dropdown {
  position: relative;
  left: 0;
  top: 100%;
}

.group-name{
  font-style: bold;
  margin-top: 1px;
}
.text {
  font-family: mandali;
}

.group {
  background-color: #f0f0f05c;
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
}

.header-left {
  flex-grow: 1;
}

.button-container {
  display: flex;
  gap: 10px;
}

.edit-button {
  border-radius: 2px;
  height: 30px;
  width: 110px;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #e3e2e2;
  color:  var(--gray);
  margin-bottom: 1px;
}

.delete-button {
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 3px;
  margin-left: auto;
  color: var(--gray);
  background-color: #e4e4e4;
  border: 1px solid #adadad;
  height: 30px;
  width: 110px;
  text-align: center;
}

.add-button {
  border-radius: 3px;
  margin-left: auto;
  color: var(--gray);
  background-color: #e3e3e3cf;
  border: 1px solid var(--base-bg);
  display: flex;
  justify-content: center;
  align-items: center;
  height: 30px;
  width: 100px;
  text-align: center;
}

.members-add-form-container {
  display: flex;
  flex-direction: column; /* Change to row */
  justify-content: space-between;
  align-items: flex-start; /* Align to the top */
  gap: 5px;
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
  display: flex; 
  flex-direction: row; 
  align-items: center; 
  gap: 0px; 
  margin-top: 5px; 
}

.add-member-input {
  flex-grow: 1; 
  height: 23px;
  border-radius: 0.5em;
  margin-right: 10px;
}

</style>





