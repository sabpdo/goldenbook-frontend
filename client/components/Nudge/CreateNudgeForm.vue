<script setup lang="ts">
import { ref } from "vue";
import { fetchy } from "../../utils/fetchy";

const time = ref("");
const toUser = ref("");
const emit = defineEmits(["refreshNudges"]);

const createNudge = async (to: string, time: string) => {
  try {
    await fetchy("/api/nudges/message", "POST", {
      body: { to, time },
    });
  } catch (_) {
    return;
  }
  emit("refreshNudges");
  emptyForm();
};
const emptyForm = () => {
  time.value = "";
};
</script>

<template>
  <form @submit.prevent="createNudge(toUser, time)">
    <label for="time">Time:</label>
    <input id="time" v-model="time" type="datetime-local" required />
    <button type="submit" class="pure-button-primary pure-button">Submit</button>
  </form>
</template>

<style scoped>
form {
  background-color: var(--base-bg);
  border-radius: 2em;
  display: flex;
  flex-direction: column;
  gap: 0.5em;
  padding: 1em;
  width: 10em;
}
</style>
