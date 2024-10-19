<script setup lang="ts">
import MessageComponent from "./MessageComponent.vue";
import { useUserStore } from "@/stores/user";
import { fetchy } from "@/utils/fetchy";
import { storeToRefs } from "pinia";
import { onBeforeMount, ref } from "vue";
import { useToastStore } from "@/stores/toast";
import SendMessageComponent from "./SendMessageForm.vue";
import Sidebar from "./MessageListComponent.vue";

const messages = ref<Array<Record<string, string>>>([]);
const { isLoggedIn, currentUsername } = storeToRefs(useUserStore());
const { toast } = storeToRefs(useToastStore());
const loaded = ref(false);
const toUser = ref("");

function setSelectedUser(username: string) {
  toUser.value = username;
}

async function getMessages(user: string) {
  let query: Record<string, string> = { currentUser: user, otherUser: toUser.value };
  let messageResults;
  if (!toUser.value) {
    toast.value = { message: "Please select a user to chat with", style: "error" };
  }
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
  <section v-if="isLoggedIn" class="main-container">
    <Sidebar @toUser="setSelectedUser" @refreshMessages="getMessages(currentUsername)" />
    <div class="messages-section">
      <h1>Messages</h1>
      <section v-if="messages.length === 0">
        <p>No messages yet</p>
      </section>
      <article v-for="message in messages" :key="message._id" class="message-container">
        <MessageComponent :message="message" @refreshMessages="getMessages(currentUsername)" />
      </article>
      <SendMessageComponent :toUser="toUser" @refreshMessages="getMessages(currentUsername)" class="send-message" />
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
  height: 100%;
}

.send-message {
  position: fixed;
  bottom: 0;
  left: 300px;
  right: 0;
  background-color: #fff;
  padding: 10px;
  box-shadow: 0 -2px 5px rgba(0, 0, 0, 0.1);
  z-index: 10;
}
</style>
