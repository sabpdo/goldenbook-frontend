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
  console.log(props.authorizers);
  try {
    await fetchy(`/api/authorize/control`, "POST", {
      body: { username: authorizer },
    });
  } catch {
    return;
  }
  console.log(props.authorizers);
  emit("refreshAuthorizers");
};

const revokeControl = async (authorizer: string) => {
  try {
    await fetchy(`/api/authorize/control`, "DELETE", {
      body: { username: authorizer },
    });
  } catch {
    return;
  }
  emit("refreshAuthorizers");
};
onBeforeMount(async () => {
  await getUsers();
  loaded.value = true;
});
</script>

<template>
  <h3>Authorizers</h3>
  <ul>
    <div v-for="user in users" :key="user.username">
      {{ user.username }}
      <button class="revoke-control" v-if="props.authorizers.includes(user.username)" @click="revokeControl(user.username)">Revoke Control</button>
      <button v-else @click="giveControl(user.username)">Give Control</button>
    </div>
  </ul>
</template>

<style scoped>
h3 {
  text-align: center;
  color: var(--nav-green);
}
.revoke-control {
  background-color: var(--green);
  color: white;
}

.revoke-control:hover {
  background-color: var(--nav-green);
  color: white;
}
</style>
