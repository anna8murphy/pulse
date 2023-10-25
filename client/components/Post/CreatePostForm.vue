<script setup lang="ts">
import { onMounted, ref } from "vue";
import { fetchy } from "../../utils/fetchy";

const title = ref("");
const note = ref("");
const link = ref("");
const hasPaywall = ref(false);

const groups = ref<Group[]>([]); 
const selectedGroups = ref<Group[]>([]); 

const emit = defineEmits(["refreshPosts"]);

interface Group {
  name: string;
}

onMounted(async () => {
  try {
    const response = await fetchy("api/groups", "GET");
    groups.value = response;
  } catch (_) {
    return;
  }
});

const createPost = async (title: string, note: string, hasPaywall: boolean, link: string) => {
  try {
    const selectedGroupNames = selectedGroups.value.map(group => group.name);
    await fetchy("api/posts", "POST", {
      body: {
        title: title,
        url: link,
        paywall: hasPaywall ? "Y" : "N",
        groups: selectedGroupNames,
        note: note,
        options: {}
      },
    });
  } catch (_) {
    return;
  }
  emit("refreshPosts");
  emptyForm();
};


const emptyForm = () => {
  title.value = "";
};

</script>

<template>
  <form @submit.prevent="createPost(title, note, hasPaywall, link)">
    <h3>What are you reading?</h3>
    <textarea id="title" v-model="title" placeholder="Paste the title here..." required></textarea>
    <textarea id="link" v-model="link" class="link-input" placeholder="URL" required></textarea>
    <label for="note">Add a note!</label>
    <textarea id="note" v-model="note" class="note-input" placeholder="Write your thoughts here..."></textarea>
    <div class="paywall-option">
      <label for="paywall">Paywall:</label>
      <input type="checkbox" id="paywall" v-model="hasPaywall" />
    </div>
    <label for="groups">Post to Groups:</label>
    <div class="group-list">
      <label v-for="group in groups" :key="group.name">
        <input type="checkbox" v-model="selectedGroups" :value="group" />
        {{ group.name }}
      </label>
    </div>
    <button type="submit" class="create-btn btn-small pure-button">Publish!</button>
  </form>
</template>

<style scoped>
form {
  background-color: rgb(249, 249, 249);
  border-radius: .5em;
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
  padding: 0.em;
  border-radius: 3px;
  resize: none;
}

.link-input {
  height: 1em;
  padding: 0.5em;
  border-radius: 4px;
}

.note-input {
  height: 3em;
}

.group-list {
  padding: .1em;
  height: 2em;
}

.paywall-option {
  display: flex;
  height: 3em;
  align-items: center;
}

input[type="checkbox"] {
  margin-left: 0.5em;
}

.button-secondary {
    background: #F25B6C;
}

.create-btn {
  justify-content: flex-end; /* Move the button to the right side */
  height: 2.5em;
  width: 6em;
  font-size: 15px;
  align-self: flex-end; /* Align the button to the right within the flex container */
  background: var(--base-bg);
  color: white;
  border-radius: 4px;
  margin-bottom: 0.5em;
}

h3 {
  margin-top: 0; /* Remove the default top margin */
  margin-bottom: 0.5em; /* Add some bottom margin for spacing */
}

</style> 


