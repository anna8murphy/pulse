<script setup lang="ts">
import CreateGroupForm from "@/components/Groups/CreateGroupForm.vue";
import EditGroupForm from "@/components/Groups/EditGroupForm.vue";
import GroupComponent from "@/components/Groups/GroupComponent.vue";
import { useUserStore } from "@/stores/user";
import { fetchy } from "@/utils/fetchy";
import { storeToRefs } from "pinia";
import { onBeforeMount, ref } from "vue";

const { isLoggedIn } = storeToRefs(useUserStore());

const loaded = ref(false);
let groups = ref<Array<Record<string, string & { members: string[] }>>>([]);
let editing = ref("");
let searchGroup = ref("");
const selectedMembers = ref<Array<{ groupId: string; selected: string[] }>>([]);

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
    <h2 v-if="!searchGroup">Groups:</h2>
    <h2 v-else>Group name {{ searchGroup }}:</h2>
    <CreateGroupForm @refreshGroups="getGroups" />
  </div>
  <section class="groups" v-if="loaded && groups.length !== 0">
    <article v-for="group in groups" :key="group._id">
      <GroupComponent v-if="editing !== group._id" :group="group" @refreshGroups="getGroups" @editGroup="updateEditing" />
      <EditGroupForm v-else: :group="group" @refreshGroups="getGroups" @editGroup="updateEditing" />
    </article>
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
  background-color: var(--base-bg);
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
  background-color: var(--base-bg);
  padding: 5px;
  border-radius: 5px;
}
</style>

