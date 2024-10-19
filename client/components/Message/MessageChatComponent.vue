<script setup lang="ts">
import MessageComponent from "./MessageComponent.vue";
import { useUserStore } from "@/stores/user";
import { fetchy } from "@/utils/fetchy";
import { storeToRefs } from "pinia";
import { onBeforeMount, ref } from "vue";
import SendMessageComponent from "./SendMessageForm.vue";
import Sidebar from "./MessageListComponent.vue";

const messages = ref<Array<Record<string, string>>>([]);
const { isLoggedIn, currentUsername } = storeToRefs(useUserStore());
const loaded = ref(false);
const selectedUser = ref("");

function setSelectedUser(username: string) {
  selectedUser.value = username;
}

async function getMessages(user: string) {
  let query: Record<string, string> = { user };
  let messageResults;
  try {
    messageResults = await fetchy("/api/messages", "GET", { query });
  } catch (_) {
    return;
  }
  messages.value = messageResults;
}

onBeforeMount(async () => {
  await getMessages(currentUsername.value);
  loaded.value = true;
});
</script>

<template>
  <section v-if="isLoggedIn && loaded" class="main-container">
    <Sidebar @toUser="setSelectedUser" />
    <div class="messages-section">
      <h1>Messages</h1>
      <article v-for="message in messages" :key="message._id">
        <MessageComponent :message="message" @refreshMessages="getMessages(currentUsername)" />
      </article>
      <SendMessageComponent :toUser="selectedUser" @refreshMessages="getMessages(currentUsername)" />
    </div>
  </section>
</template>

<style scoped>
h1 {
  text-align: center;
  margin-bottom: 20px;
}

.main-container {
  display: flex;
  align-items: flex-start;
  height: calc(100vh - 80px);
}

.sidebar {
  width: 250px;
  background-color: #f4f4f4;
  border-right: 1px solid #ddd;
  height: 100%;
  overflow-y: auto;
}

.messages-section {
  flex: 1;
  display: flex;
  flex-direction: column;
  padding: 20px;
  background-color: #fff;
  overflow-y: auto;
  height: 100%;
}

.messages-section article {
  margin-bottom: 10px;
}
</style>
