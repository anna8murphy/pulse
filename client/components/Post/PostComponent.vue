<script setup lang="ts">
import { useUserStore } from "@/stores/user";
import { formatDate } from "@/utils/formatDate";
import { storeToRefs } from "pinia";
import { fetchy } from "../../utils/fetchy";

const props = defineProps(["post"]);
const emit = defineEmits(["editPost", "refreshPosts"]);
const { currentUsername } = storeToRefs(useUserStore());
const isAuthor = (props.post.author === currentUsername.value);

const deletePost = async () => {
  try {
    await fetchy(`api/posts/${props.post._id}`, "DELETE");
  } catch {
    return;
  }
  emit("refreshPosts");
};

const formatLink = (link: string) => {
  if (link) {
    if (!link.startsWith('http://') && !link.startsWith('https://')) {
      return 'http://' + link;
    }
  }
  return link;
};

const extractSourceFromLink = (link: string) => {
  const hostnameRegex = /:\/\/(www[0-9]?\.)?(.[^/:]+)/i;
  const matches = link.match(hostnameRegex);

  if (matches && matches.length > 2) {
    return matches[2]; 
  }
  return 'not specified'; 
};
const formattedLink = formatLink(props.post.link?.link);
const source = extractSourceFromLink(formattedLink);
</script>

<template>
  <p class="author">{{ props.post.author }}</p>
  <h3>
    <a :href="formattedLink" target="_blank" v-if="props.post.link?.link">
      {{ props.post.title }}
    </a>
    <span v-else>
      {{ props.post.title }}
    </span>
  </h3>
  <p>{{ source ? 'source: ' + source : '' }}</p>
  <p>{{ "paywall: " + (props.post.link?.paywall ? "true" : "false") }}</p>
  <p>{{ "note: " + props.post.note?.note }}</p>
  <p v-if="isAuthor">groups: {{ props.post.groups }}</p>
  <div class="base">
    <menu v-if="props.post.author == currentUsername">
      <li><button class="edit-note btn-small pure-button" @click="emit('editPost', props.post._id)">Edit Note</button></li>
      <li><button class="button-error btn-small pure-button" @click="deletePost">Delete</button></li>
    </menu>
    <article class="timestamp">
      <p v-if="props.post.dateCreated !== props.post.dateUpdated">Edited on: {{ formatDate(props.post.dateUpdated) }}</p>
      <p v-else>Created on: {{ formatDate(props.post.dateCreated) }}</p>
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

.edit-note {
  background-color: #e3e2e2;
  color:  #827f7f;
}
</style>
