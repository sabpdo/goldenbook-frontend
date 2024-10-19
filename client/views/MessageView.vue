<script setup lang="ts">
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";
import MessageListComponent from "@/components/Message/MessageListComponent.vue";
import MessageChatComponent from "@/components/Message/MessageChatComponent.vue";

const { currentUsername, isLoggedIn } = storeToRefs(useUserStore());
</script>

<template>
  <section>
    <h1>Messages</h1>
    <MessageChatComponent />
    <MessageListComponent />
  </section>
</template>

<style scoped>
h1 {
  text-align: center;
}
</style>
