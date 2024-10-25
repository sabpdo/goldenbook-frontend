<script setup lang="ts">
import { useUserStore } from "@/stores/user";
import { formatDate } from "@/utils/formatDate";
import { storeToRefs } from "pinia";
import { fetchy } from "../../utils/fetchy";
import { ref, defineProps, defineEmits, onUnmounted } from "vue";

const props = defineProps(["post"]);
const emit = defineEmits(["editPost", "refreshPosts"]);
const { currentUsername } = storeToRefs(useUserStore());
const isSpeaking = ref(false);
let synth = window.speechSynthesis;

const toggleSpeakContent = () => {
  if (isSpeaking.value) {
    synth.cancel();
    isSpeaking.value = false;
    return;
  }

  const utterance = new SpeechSynthesisUtterance(props.post.content);
  utterance.onend = () => (isSpeaking.value = false);
  synth.speak(utterance);
  isSpeaking.value = true;
};

onUnmounted(() => {
  synth.cancel();
  isSpeaking.value = false;
});

const deletePost = async () => {
  try {
    await fetchy(`/api/posts/${props.post._id}`, "DELETE");
  } catch {
    return;
  }
  emit("refreshPosts");
};
</script>

<template>
  <div class="post-container">
    <div class="post-header">
      <p class="author">{{ props.post.author }}</p>
      <button class="audio" @click="toggleSpeakContent">
        <img src="@/assets/images/audio.png" alt="sound icon" />
      </button>
    </div>
    <p>{{ props.post.content }}</p>
    <div class="base">
      <menu v-if="props.post.author == currentUsername">
        <li><button class="btn-small pure-button" @click="emit('editPost', props.post._id)">Edit</button></li>
        <li><button class="button-error btn-small pure-button" @click="deletePost">Delete</button></li>
      </menu>
      <article class="timestamp">
        <p v-if="props.post.dateCreated !== props.post.dateUpdated">Edited on: {{ formatDate(props.post.dateUpdated) }}</p>
        <p v-else>Created on: {{ formatDate(props.post.dateCreated) }}</p>
      </article>
    </div>
  </div>
</template>

<style scoped>
p {
  margin: 0em;
}

img {
  width: 20px;
  height: 20px;
}

.post-container {
  display: flex;
  flex-direction: column;
  gap: 0.5em;
}

.post-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.audio {
  display: flex;
  justify-content: flex-end;
  align-items: right;
  gap: 0.3em;
  padding: 6px;
  color: #fff;
  border: none;
  border-radius: 1px;
  font-size: 1em;
  font-weight: bold;
  cursor: pointer;
  max-width: 50px;
  border: 2px solid var(--green);
  border-radius: 20px;
}

.audio:hover {
  background-color: nav(--green);
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.2);
}

.audio:active {
  background-color: #388e3c;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
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

p {
  padding: 5px;
}
</style>
