<script setup lang="ts">
import { fetchy } from "@/utils/fetchy";
import { ref } from "vue";

const time = ref("");
const props = defineProps(["toUser"]);
const emit = defineEmits(["refreshNudges"]);

const createNudge = async (time: string, to: string) => {
  console.log(to, time);
  try {
    await fetchy("/api/nudges/message", "POST", {
      body: { to: to, time: time },
    });
  } catch {
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
  <form @submit.prevent="createNudge(time, props.toUser)">
    <label for="time">Nudge to Message at:</label>
    <input id="time" v-model="time" type="datetime-local" required />
    <button type="submit">Send</button>
  </form>
</template>

<style scoped>
.nudge-form {
  background: linear-gradient(135deg, #e0f7fa, #80deea);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

label {
  margin-right: 1em;
  font-weight: bold;
}

input[type="datetime-local"] {
  flex: 1;
  border: 1px solid #ddd;
  border-radius: 0.5em;
  padding: 0.5em;
}
button {
  background: var(--green);
  color: white;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  padding: 10px 15px;
  transition: background 0.3s;
}
button:hover {
  background: var(--nav-green);
}
</style>
