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
      console.log("HERE");
        return 'http://' + link;      
    }
  }
  return link;
};

const extractSourceFromLink = (link: string) => {
  const hostnameRegex = /:\/\/(www[0-9]?\.)?(.[^/:]+)/i;
  const matches = link.match(hostnameRegex);

  if (matches && matches.length > 2) {
    if (props.post.link?.paywall) {
      return matches[2] + ' ($)'; 
    }
    else {
      return matches[2]; 
    }
  }
  return 'not specified'; 
};
const formattedLink = formatLink(props.post.link?.link);
const source = extractSourceFromLink(formattedLink);

</script>

<template>
  <p class="author">{{ props.post.author }}</p>
  <h2>
    <a :href="formattedLink" target="_blank" v-if="props.post.link?.link">
      {{ props.post.title }}
    </a>
    <span v-else>
      {{ props.post.title }}
    </span>
  </h2>
    <p class="source">{{ source ? 'source: ' + source : '' }}</p>
    <p class="note">{{ props.post.note?.note }}</p>
  <div class="base">
    <menu v-if="props.post.author == currentUsername">
      <li><button class="edit-note btn-small pure-button" @click="emit('editPost', props.post._id)">Edit Note</button></li>
      <li><button class="delete-button btn-small pure-button" @click="deletePost">Delete</button></li>
    </menu>
    <article class="timestamp">
      <p v-if="props.post.dateCreated !== props.post.dateUpdated">Edited on: {{ formatDate(props.post.dateUpdated) }}</p>
      <p v-else>Created on: {{ formatDate(props.post.dateCreated) }}</p>
      <p v-if="props.post.author == currentUsername"><br>{{ "posted to: " +  props.post.groups.join(', ')}}</p>
    </article>
  </div>
</template>

<style scoped>
p {
  margin: 0em;
}

.author {
  font-weight: bold;
  font-size: 1.3em;
}

.note{
    /* background-color: #e4e4e45e; */
    border: 1px solid #e0e0e077; 
    color: var(--gray);
    border-radius: 2px;
    padding: 10px;
    margin: 10px 0;
    width: 25em;
}

menu {
  list-style-type: none;
  display: flex;
  flex-direction: row;
  gap: 1em;
  padding: 0;
  margin: 0;
}

p {
  font-family: mandali;
}
.source {
  font-style: italic;
}

.timestamp {
    display: block;
    font-size: 0.9em;
    font-style: italic;
  }

.base {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.delete-button {
  border-radius: 2px;
  margin-right: auto;
  color: rgb(249, 249, 249);
  background-color: var(--red);
}

.base article:only-child {
  margin-left: auto;
}

.edit-note {
  background-color: #e3e3e3cf;
  color: var(--gray);
}

h2 {
  font-family: crimson;
  margin-top: 0; 
  margin-bottom: 0.5em;
  font-size: 1.3em;
}

a:visited {
  color: #2265d2;
}

a {
  color: var(--base-bg);
}

</style>
