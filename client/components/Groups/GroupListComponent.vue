<script setup lang="ts">
import CreateGroupForm from "@/components/Groups/CreateGroupForm.vue";
import EditGroupForm from "@/components/Groups/EditGroupForm.vue";
import GroupComponent from "@/components/Groups/GroupComponent.vue";
import { fetchy } from "@/utils/fetchy";
import { onBeforeMount, ref } from "vue";

const loaded = ref(false);
let groups = ref<Array<Record<string, string & { members: string[] }>>>([]);
let editing = ref("");
let searchGroup = ref("");

const emit = defineEmits(["refreshGroups"]);

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

function updateEditing(id: string) {
  editing.value = id;
}

onBeforeMount(async () => {
  await (getGroups());
  loaded.value = true;
});


</script>

<template>
  <div class="row">
    <h2>Create a group {{ searchGroup }}:</h2>
    <CreateGroupForm @refreshGroups="getGroups" />
  </div>
  <section class="groups" v-if="loaded && groups.length !== 0">
    <div v-for="group in groups" :key="group._id">
      <GroupComponent v-if="editing !== group._id" :group="group" @refreshGroups="getGroups" @editGroup="updateEditing" />
      <EditGroupForm v-else: :group="group" @refreshGroups="getGroups" @editGroup="updateEditing" />
    </div>
  </section>
  <p v-else-if="loaded">No groups found</p>
  <p v-else>Loading...</p>
</template>


<style scoped>
section {
  display: flex;
  flex-direction: column;
  gap: 1em;
}

section,
p,
.row {
  margin: 0 auto;
  max-width: 60em;
}

article {
  background-color: #e2e2e2;
  border-radius: 1em;
  display: flex;
  flex-direction: column;
  gap: 0.5em;
  padding: 1em;
}

.groups {
  padding: 1em;
}

.row {
  display: flex;
  justify-content: space-between;
  margin: 0 auto;
  max-width: 60em;
}

.member-list {
  list-style: none;
  padding: 0;
  display: flex;
  flex-wrap: wrap;
  gap: 1px; /* Adjust the gap as needed */
}

.member-list li {
  background-color: #ffffff;;
  padding: 5px;
  border-radius: 5px;
}

</style>

