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
    <textarea v-model="message" id="message" name="message" rows="4" cols="50" required></textarea>
    <button type="submit" class="btn-small pure-button">Send</button>
  </form>
</template>

<style scoped>
.message-form {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 1rem;
}
</style>
