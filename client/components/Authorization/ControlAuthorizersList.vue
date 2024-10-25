<script setup lang="ts">
import { defineEmits, defineProps, onBeforeMount, ref } from "vue";
import { fetchy } from "../../utils/fetchy";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";

const { currentUsername } = storeToRefs(useUserStore());
const users = ref<Array<Record<string, string>>>([]);
const props = defineProps(["authorizers"]);
const emit = defineEmits(["refreshAuthorizers"]);
const loaded = ref(false);

async function getUsers() {
  let userResults;
  try {
    userResults = await fetchy("/api/users", "GET");
  } catch {
    return;
  }
  users.value = userResults.filter((user: any) => user.username != currentUsername.value);
}

const giveControl = async (authorizer: string) => {
  try {
    await fetchy(`/api/authorize/control`, "POST", {
      body: { username: authorizer },
    });
  } catch {
    return;
  }
  emit("refreshAuthorizers");
};

const revokeControl = async (authorizer: string) => {
  try {
    await fetchy(`/api/authorize/control/${authorizer}`, "DELETE");
  } catch {
    return;
  }
  console.log(props.authorizers);
  emit("refreshAuthorizers");
};
onBeforeMount(async () => {
  await getUsers();
  loaded.value = true;
});
</script>

<template>
  <section class="authorizers-container">
    <h3>Authorizers</h3>
    <ul class="user-list">
      <div v-for="user in users" :key="user.username" class="user-card">
        <span class="username">{{ user.username }}</span>
        <button class="control-button revoke-control" v-if="props.authorizers.includes(user.username)" @click="revokeControl(user.username)">Revoke Control</button>
        <button class="control-button give-control" v-else @click="giveControl(user.username)">Give Control</button>
      </div>
    </ul>
  </section>
</template>
<style scoped>
.authorizers-container {
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
  margin-bottom: 20px;
}

.user-list {
  list-style-type: none;
  padding: 0;
}

.user-card {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  margin-bottom: 10px;
  background-color: white;
  border-radius: 8px;
  box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.1);
}

.username {
  font-weight: bold;
  color: #333;
}

.control-button {
  padding: 8px 16px;
  border: none;
  border-radius: 5px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s;
}

.revoke-control {
  background-color: var(--red);
  color: white;
}

.revoke-control:hover {
  background-color: darkred;
}

.give-control {
  background-color: var(--green);
  color: white;
}

.give-control:hover {
  background-color: var(--nav-green);
}
</style>
