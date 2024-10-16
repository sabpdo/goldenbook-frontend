<script setup lang="ts">
import PostListComponent from "@/components/Message/MessageListComponent.vue";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";

const { currentUsername, isLoggedIn } = storeToRefs(useUserStore());
</script>

<template>
  <main>
    <h1>Messags</h1>
    <section>
      <h1 v-if="isLoggedIn">Welcome {{ currentUsername }}!</h1>
      <h1 v-else>Please login!</h1>
    </section>
    <MessageListComponent />
  </main>
</template>

<style scoped>
h1 {
  text-align: center;
}
</style>
