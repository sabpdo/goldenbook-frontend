<script setup lang="ts">
import { ref } from "vue";
import { fetchy } from "../../utils/fetchy";
import { storeToRefs } from "pinia";
const action = ref("");
const emit = defineEmits(["refreshRecords"]);
import { useToastStore } from "@/stores/toast";
const { toast } = storeToRefs(useToastStore());

const createRecord = async (action: string) => {
  if (!validateAction()) {
    return;
  }
  try {
    await fetchy("/api/records", "POST", {
      body: { action },
    });
  } catch {
    return;
  }
  emit("refreshRecords");
  emptyForm();
};

const validateAction = () => {
  if (!action.value.trim()) {
    toast.value = { message: "Action cannot be empty.", style: "error" };
    return false;
  }
  if (action.value.length > 50) {
    toast.value = { message: "Action cannot exceed 50 characters.", style: "error" };
    return false;
  }
  toast.value = null;
  return true;
};

const emptyForm = () => {
  action.value = "";
};
</script>

<template>
  <form @submit.prevent="createRecord(action)">
    <label for="action">What daily habit did you build today?</label>
    <textarea id="action" v-model="action" maxlength="50" placeholder="Record an action!" required> </textarea>
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
  border: 2px solid var(--green);
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
label {
  font-weight: bold;
  color: var(--nav-green);
}
</style>
