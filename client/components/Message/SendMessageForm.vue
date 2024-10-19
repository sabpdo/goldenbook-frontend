<script setup lang="ts">
import { fetchy } from "@/utils/fetchy";
import { ref } from "vue";

const message = ref("");
const props = defineProps(["toUser"]);
const emit = defineEmits(["refreshMessages"]);

async function sendMessage(message: string, to: string) {
  try {
    await fetchy("/api/messages", "POST", { body: { content: message, to: to } });
  } catch {
    return;
  }
  emit("refreshMessages");
  emptyForm();
}
const emptyForm = () => {
  message.value = "";
};
</script>

<template>
  <form @submit.prevent="sendMessage(message, props.toUser)" class="message-form">
    <textarea v-model="message" id="message" name="message" rows="4" required placeholder="Type your message here..." class="message-input"></textarea>
    <button type="submit" class="btn-small pure-button send-button">✉️</button>
  </form>
</template>

<style scoped>
.message-form {
  display: flex;
  align-items: flex-start;
  border: 2px solid var(--green);
}

.message-input {
  flex: 1;
  resize: none;
  padding: 10px;
  border: 1px solid #ddd;
  margin-right: 10px;
}

.send-button {
  background: var(--green);
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  padding: 10px 15px;
  transition: background 0.3s;
}

.send-button:hover {
  background: var(--nav-green);
}
</style>
