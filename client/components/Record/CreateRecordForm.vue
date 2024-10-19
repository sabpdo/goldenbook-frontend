<script setup lang="ts">
import { ref } from "vue";
import { fetchy } from "../../utils/fetchy";

const action = ref("");
const emit = defineEmits(["refreshRecords"]);

const createRecord = async (action: string) => {
  try {
    await fetchy("/api/records", "POST", {
      body: { action },
    });
  } catch (_) {
    return;
  }
  emit("refreshRecords");
  emptyForm();
};

const emptyForm = () => {
  action.value = "";
};
</script>

<template>
  <form @submit.prevent="createRecord(action)">
    <label for="action">Action:</label>
    <textarea id="action" v-model="action" placeholder="Record an action!" required> </textarea>
    <button type="submit">Submit</button>
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
  width: 10em;
  border: 2px solid #00796b;
  border-radius: 20px;
}

textarea {
  font-family: inherit;
  font-size: inherit;
  height: 1em;
  padding: 0.5em;
  border-radius: 4px;
  resize: none;
}
</style>
