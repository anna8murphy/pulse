<script setup lang="ts">
import { onMounted, ref } from "vue";
import { fetchy } from "../../utils/fetchy";

const title = ref("");
const note = ref("");
const link = ref("");
const hasPaywall = ref(false);

const groups = ref<Group[]>([]); 

const selectedGroups = ref([]);

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
    await fetchy("api/posts", "POST", {
      body: {
        title: title,
        url: link,
        paywall: hasPaywall ? "Y" : "N",
        groups: "",
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

// const addToGroups = async () => {
//   try {
//     const postData = {
//       title: title.value,
//       hasPaywall: hasPaywall.value,
//       link: link.value,
//       note: note.value,
//       groups: selectedGroups.value,
//     };
//     await fetchy("api/posts/addToGroups", "POST", { body: postData });
//   } catch (_) {
//     return;
//   }
//   emit("refreshPosts");
//   emptyForm();
// };

const emptyForm = () => {
  title.value = "";
};

</script>

<template>
  <form @submit.prevent="createPost(title, note, hasPaywall, link)">
    <label for="title">Article Upload:</label>
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
        <input type="checkbox" v-model="selectedGroups" :value="group.name" />
        {{ group.name }}
      </label>
    </div>
    <button type="submit" class="pure-button-primary pure-button">Create Post</button>
  </form>
</template>

<style scoped>
form {
  background-color: var(--base-bg);
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

</style> 


