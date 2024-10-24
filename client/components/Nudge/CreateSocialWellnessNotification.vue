<script setup lang="ts">
import { ref } from "vue";
import { fetchy } from "../../utils/fetchy";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";

const start = ref("");
const end = ref("");
const frequency = ref("");
const emit = defineEmits(["refreshNudges"]);
const isReminderOn = ref(false);
const { currentUsername } = storeToRefs(useUserStore());

const createPeriodicNudge = async (start: string, end: string, frequency: string, to: string) => {
  try {
    await fetchy("/api/nudges/message/periodic", "POST", {
      body: { start, end, frequency, to },
    });
  } catch {
    return;
  }
  emit("refreshNudges");
  emptyForm();
};
const emptyForm = () => {
  start.value = "";
  end.value = "";
  frequency.value = "";
  isReminderOn.value = false;
};
</script>

<template>
  <form @submit.prevent="createPeriodicNudge(start, end, frequency, currentUsername)">
    <div class="row">
      <label for="start">Start:</label>
      <input id="start" v-model="start" type="datetime-local" required />

      <label for="end">End:</label>
      <input id="end" v-model="end" type="datetime-local" required />
    </div>
    <div class="row">
      <label for="frequency">Frequency:</label>
      <select id="frequency" v-model="frequency" required>
        <option value="1">Daily</option>
        <option value="7">Weekly</option>
        <option value="30">Monthly</option>
      </select>
      <button type="submit">Submit</button>
    </div>
  </form>
</template>

<style scoped>
input {
  border: 1px solid #ddd;
  border-radius: 0.5em;
  border-color: var(--green);
  padding: 0.5em;
  max-width: 120px;
  align-self: flex-start;
  align-items: center;
}
form {
  border-radius: 2em;
  display: flex;
  flex-direction: column;
  gap: 1.2em;
  padding: 1.5em;
  width: 100%;
  max-width: 400px;
  margin: 0 auto;
}
select {
  border: 1px solid #ddd;
  border-radius: 0.5em;
  padding: 0.5em;
  max-width: 100px;
  align-self: flex-start;
  align-items: center;
  border-color: var(--green);
}
label {
  margin-right: 1em;
  font-weight: bold;
  margin-left: 1em;
}
.row {
  margin-right: 5em;
}
button {
  width: 100%;
}
.toggle-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-top: 1em;
}

.toggle-label {
  font-size: 0.9em;
  margin-right: 1em;
  flex: 1;
}

.switch {
  position: relative;
  display: inline-block;
  width: 40px;
  height: 20px;
}

.switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #ccc;
  transition: 0.4s;
  border-radius: 34px;
}

.slider:before {
  position: absolute;
  content: "";
  height: 12px;
  width: 12px;
  left: 4px;
  bottom: 4px;
  background-color: white;
  transition: 0.4s;
  border-radius: 50%;
}

input:checked + .slider {
  background-color: var(--green);
}

input:checked + .slider:before {
  transform: translateX(20px);
}

.slider.round {
  border-radius: 34px;
}
</style>
