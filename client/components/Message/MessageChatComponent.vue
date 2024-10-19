<script setup lang="ts">
import MessageComponent from "./MessageComponent.vue";
import { useUserStore } from "@/stores/user";
import { fetchy } from "@/utils/fetchy";
import { storeToRefs } from "pinia";
import { onBeforeMount, ref } from "vue";

const messages = ref<Array<Record<string, string>>>([]);
const { isLoggedIn, currentUsername } = storeToRefs(useUserStore());
const loaded = ref(false);

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
  <section v-if="isLoggedIn && loaded && messages.length !== 0" class="message-container">
    <article v-for="message in messages" :key="message._id">
      <MessageComponent :message="message" @refreshMessages="getMessages(currentUsername)" />
    </article>
  </section>
</template>

<style scoped>
.message-container {
  display: flex;
  flex-direction: column;
}
</style>
