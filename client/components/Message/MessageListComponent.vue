<script setup lang="ts">
import { useUserStore } from "@/stores/user";
import { fetchy } from "@/utils/fetchy";
import { storeToRefs } from "pinia";
import { onBeforeMount, ref } from "vue";

const users = ref<Array<Record<string, string>>>([]);
const loaded = ref(false);
const { isLoggedIn } = storeToRefs(useUserStore());
const emit = defineEmits(["toUser", "refreshMessages"]);
const selectedUser = ref<string | null>(null);

async function getUsers() {
  let userResults;
  try {
    userResults = await fetchy("/api/users", "GET");
  } catch (_) {
    return;
  }
  users.value = userResults;
}
onBeforeMount(async () => {
  await getUsers();
  loaded.value = true;
});
function selectUser(username: string) {
  selectedUser.value = username;
  emit("toUser", username);
  emit("refreshMessages");
}
</script>

<template>
  <div class="sidebar" v-if="isLoggedIn && loaded">
    <h2>Select User</h2>
    <div class="user-container">
      <div class="user-block" v-for="user in users" :key="user.id" @click="selectUser(user.username)" :class="{ selected: user.username === selectedUser }">
        {{ user.username }}
      </div>
    </div>
  </div>
</template>

<style scoped>
.sidebar {
  width: 250px;
  background-color: #f4f4f4;
  border-right: 1px solid #ddd;
  height: 100vh;
  overflow-y: auto;
  padding: 20px;
}
.sidebar h2 {
  margin: 0 0 20px;
  font-size: 1.5em;
  color: #333;
}

.user-container {
  display: flex;
  flex-direction: column;
}

.user-block {
  background-color: #ffffff;
  border: 1px solid #ddd;
  border-radius: 10px;
  padding: 15px;
  margin-bottom: 10px;
  cursor: pointer;
  transition:
    background-color 0.3s,
    transform 0.2s;
}

.user-block:hover {
  background-color: #e0e0e0;
  transform: scale(1.02);
}

.selected {
  background-color: #d0d0ff;
  font-weight: bold;
  color: #000;
}
</style>
