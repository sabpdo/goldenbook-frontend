<script setup lang="ts">
import { useUserStore } from "@/stores/user";
import { formatDate } from "@/utils/formatDate";
import { storeToRefs } from "pinia";
import { fetchy } from "../../utils/fetchy";

const props = defineProps(["message", "toUser"]);
const emit = defineEmits(["editMessage", "refreshMessages"]);
const { currentUsername } = storeToRefs(useUserStore());

const deleteMessage = async () => {
  try {
    await fetchy(`/api/messages/${props.message._id}`, "DELETE");
  } catch {
    return;
  }
  emit("refreshMessages");
};
</script>

<template>
  <div
    :class="{
      'message-sent': props.message.from === currentUsername,
      'message-received': props.message.to == currentUsername,
    }"
  >
    <p
      class="message-content"
      :style="{
        backgroundColor: props.message.to == currentUsername ? 'lightblue' : 'grey',
      }"
    >
      {{ props.message.content }}
    </p>
    <div class="base">
      <menu v-if="props.message.from == currentUsername">
        <li>
          <button class="button-error btn-small pure-button" @click="deleteMessage">Delete</button>
        </li>
      </menu>
      <article class="timestamp">
        <p v-if="props.message.dateCreated !== props.message.dateUpdated">Edited on: {{ formatDate(props.message.dateUpdated) }}</p>
        <p v-else>Sent: {{ formatDate(props.message.dateCreated) }}</p>
      </article>
    </div>
  </div>
</template>
<style scoped>
.message-sent {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}

.message-received {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.message-content {
  margin: 0;
  word-wrap: break-word;
  padding: 7px;
  border-radius: 10px;
  display: inline-block;
}

.timestamp {
  font-size: 0.7em;
  color: #888;
  opacity: 0;
  transition: opacity 0.3s ease;
  visibility: hidden;
}

.message-sent:hover .timestamp,
.message-received:hover .timestamp {
  opacity: 1;
  visibility: visible;
}

.base {
  display: flex;
}
</style>
