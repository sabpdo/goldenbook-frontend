<script setup lang="ts">
import { fetchy } from "../../utils/fetchy";
import { defineProps, ref, onBeforeMount } from "vue";
const props = defineProps(["authorizee"]);
const allowedActions = ref([""]);
const loaded = ref(false);
const allPossibleActions = ["Post", "Message", "Record", "Nudge"];

const getAuthorizedActions = async () => {
  let query = { username: props.authorizee };
  try {
    const denied_actions = await fetchy(`/api/authorize/deny/user`, "GET", { query });
    const denied_action_list = denied_actions.map((action: { denied_action: string }) => action.denied_action);
    allowedActions.value = allPossibleActions.filter((action) => !denied_action_list.includes(action));
  } catch {
    return;
  }
};

const toggleAuthorization = async (username: string, action: string, allowed: boolean) => {
  try {
    if (allowed) {
      await fetchy(`/api/authorize/deny/${action.toLowerCase()}`, "POST", {
        body: { username },
      });
    } else {
      await fetchy(`/api/authorize/allow/${action.toLowerCase()}`, "POST", {
        body: { username },
      });
    }
  } catch {
    return;
  }
  await getAuthorizedActions();
};

onBeforeMount(async () => {
  await getAuthorizedActions();
  loaded.value = true;
});
</script>

<template>
  <div v-if="loaded" class="authorizee-container">
    <label class="name">{{ props.authorizee }}</label>
    <ul>
      <li v-for="action in allPossibleActions" :key="action" class="action-item">
        <span class="action-label">{{ action }}</span>
        <label class="switch">
          <input type="checkbox" :checked="allowedActions.includes(action)" @change="toggleAuthorization(props.authorizee, action, allowedActions.includes(action))" />
          <span class="slider"></span>
        </label>
      </li>
    </ul>
  </div>
</template>

<style scoped>
h3 {
  text-align: center;
  color: var(--nav-green);
  margin-bottom: 20px;
}

.name {
  color: var(--nav-green);
  margin-bottom: 20px;
  font-weight: bold;
}

.authorizee-container {
  margin-bottom: 30px;
  max-width: 600px;
}

h2 {
  margin: 10px 0;
  color: #333;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  margin: 10px 0;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.action-item {
  padding: 10px;
  border: 1px solid #e0e0e0;
  border-radius: 5px;
  background-color: #f9f9f9;
  transition: background-color 0.2s;
}

.action-item:hover {
  background-color: #f1f1f1;
}

.action-label {
  font-weight: bold;
}

.switch {
  position: relative;
  display: inline-block;
  width: 34px;
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
  background-color: var(--red);
  transition: 0.4s;
  border-radius: 34px;
}

.slider:before {
  position: absolute;
  content: "";
  height: 14px;
  width: 14px;
  border-radius: 50%;
  left: 3px;
  bottom: 3px;
  background-color: white;
  transition: 0.4s;
}

input:checked + .slider {
  background-color: #4caf50;
}

input:checked + .slider:before {
  transform: translateX(14px);
}
</style>
