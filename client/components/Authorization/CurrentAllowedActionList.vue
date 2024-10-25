<script setup lang="ts">
import { onBeforeMount, ref } from "vue";
import { fetchy } from "../../utils/fetchy";

const allowedActions = ref([""]);
const loaded = ref(false);
const allPossibleActions = ["Post", "Mesage", "Record", "Nudge"];

const getAuthorizedActions = async () => {
  try {
    const denied_actions = await fetchy("/api/authorize", "GET");
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
  <section v-if="loaded" class="actions-container">
    <h3>Your Allowed Actions</h3>
    <ul>
      <li v-for="action in allPossibleActions" :key="action">
        {{ action }}
        <span v-if="allowedActions.includes(action)">✅</span>
        <span v-else>❌</span>
      </li>
    </ul>
  </section>
  <section v-else>
    <p>Loading actions...</p>
  </section>
</template>

<style scoped>
.actions-container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 10px;
  box-shadow: 0px 4px 8px var(--light-green);
  margin-bottom: 30px;
  border: 1px solid var(--light-green);
}

h3 {
  text-align: center;
  color: var(--nav-green);
}
ul {
  list-style-type: none;
  padding: 0;
}

li {
  text-align: center;
  font-size: 1.2rem;
  margin-bottom: 0.5rem;
}

span {
  margin-left: 0.5rem;
}
</style>
