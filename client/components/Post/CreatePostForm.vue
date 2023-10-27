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
  note.value = "";
  link.value="";
};

</script>

<template>
  <form @submit.prevent="createPost(title, note, hasPaywall, link)">
    <h3 class="shimmer-text">what are you reading?</h3>
    <textarea id="title" v-model="title" class="text" placeholder=" paste the title here..." required></textarea>
    <textarea id="link" v-model="link" class="text" placeholder=" URL" required></textarea>
    <label class="note-label" for="note"><strong>add a note!</strong></label>
    <textarea id="note" v-model="note" class="text note-input" placeholder=" write your thoughts here..."></textarea>
    <div class="paywall-option">
      <label class="text" for="paywall"><strong>paywall?</strong></label>
      <input type="checkbox" id="paywall" v-model="hasPaywall" />
    </div>
    <label class="text" for="groups"><strong>groups (optional):</strong></label>
    <div class="text" v-if="groups.length === 0"><em>please create your first group!</em></div>
    <div class="group-list">
      <label v-for="group in groups" :key="group.name" class="text">
        <input type="checkbox" v-model="selectedGroups" :value="group" />
        {{ group.name }}
      </label>
    </div>
    <button type="submit" class="btn-small pure-button moving-border-btn">Publish!</button>
  </form>
</template>

<style scoped>

.moving-border-btn {
  justify-content: flex-end; /* Move the button to the right side */
  height: 2.5em;
  width: 6em;
  font-size: 15px;
  align-self: flex-end; /* Align the button to the right within the flex container */
  background: var(--pinkred);
  border: 1px solid #f16c6c;
  color: white;
  margin-bottom: 0.5em;
  border-radius: .25em;
}

form {
  background-color: rgb(249, 249, 249);
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
  padding: 0.em;
  border-radius: 3px;
  resize: none;
}

.group-list {
  padding: .1em;
  height: 2em;
}

.note-input {
  height: 3em;
}
.text {
  font-family: mandali;
}

.note-label{
  font-family: mandali;
  /* padding: 1em; */
}
.paywall-option {
  display: flex;
  height: 3em;
  align-items: center;
}

input[type="checkbox"] {
  margin-left: 0.5em;
}

h3 {
 font-family: mandali;
  margin-top: 0; /* Remove the default top margin */
  margin-bottom: 0.2em; /* Add some bottom margin for spacing */
}

</style> 


