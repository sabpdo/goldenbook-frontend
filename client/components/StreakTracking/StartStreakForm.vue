<script setup lang="ts">
import { fetchy } from "../../utils/fetchy";

const emit = defineEmits(["refreshRecords"]);

const startTrackingMessaging = async () => {
  try {
    await fetchy("/api/records", "POST");
  } catch (_) {
    return;
  }
  emit("refreshRecords");
  emptyForm();
};

const startTrackingPosting = async () => {
  try {
    await fetchy("/api/posts", "POST");
  } catch (_) {
    return;
  }
  emit("refreshRecords");
  emptyForm();
};

const emptyForm = () => {};
</script>

<template>
  <form @submit.prevent="startTrackingMessaging">
    <button type="submit" class="pure-button-primary pure-button">Start Messaging</button>
  </form>
  <form @submit.prevent="startTrackingPosting">
    <button type="submit" class="pure-button-primary pure-button">Start Posting</button>
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
</style>
