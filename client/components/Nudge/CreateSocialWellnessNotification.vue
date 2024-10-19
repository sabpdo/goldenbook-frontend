<script setup lang="ts">
import { ref } from "vue";
import { fetchy } from "../../utils/fetchy";

const start = ref("");
const end = ref("");
const frequency = ref("");
const toUser = ref("");
const emit = defineEmits(["refreshNudges"]);

const createPeriodicNudge = async (start: string, end: string, frequency: string, to: string) => {
  try {
    await fetchy("/api/nudges/message/periodic", "POST", {
      body: { start, end, frequency, to },
    });
  } catch (_) {
    return;
  }
  emit("refreshNudges");
  emptyForm();
};
const emptyForm = () => {
  start.value = "";
  end.value = "";
  frequency.value = "";
};
</script>

<template>
  <form @submit.prevent="createPeriodicNudge(start, end, frequency, toUser)">
    <label for="start">Start:</label>
    <input id="start" v-model="start" type="datetime-local" required />
    <label for="end">End:</label>
    <input id="end" v-model="end" type="datetime-local" required />
    <label for="frequency">Frequency:</label>
    <input id="frequency" v-model="frequency" type="text" required />
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
