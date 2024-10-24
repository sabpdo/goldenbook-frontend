<script setup lang="ts">
import { onBeforeMount, ref } from "vue";
import { fetchy } from "../../utils/fetchy";

const allowedActions = ref([""]);
const loaded = ref(false);
const allPossibleActions = ["Post", "Mesage", "Record", "Nudge"];

const getAuthorizedActions = async () => {
  try {
    const denied_actions = await fetchy(`/api/authorize`, "GET");
    allowedActions.value = allPossibleActions.filter((action) => !denied_actions.includes(action));
  } catch {
    return;
  }
};
onBeforeMount(async () => {
  await getAuthorizedActions();
  loaded.value = true;
});
</script>

<template>
  <h3>Your Allowed Actions</h3>
  <div v-for="action in allPossibleActions" :key="action">
    {{ action }}
    <span v-if="allowedActions.includes(action)">✔️</span>
    <span v-else>❌</span>
  </div>
</template>

<style scoped>
h3 {
  text-align: center;
  color: var(--nav-green);
}
</style>
