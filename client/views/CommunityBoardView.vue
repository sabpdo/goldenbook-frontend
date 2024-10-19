<script setup lang="ts">
import PostListComponent from "@/components/Post/PostListComponent.vue";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";

const { currentUsername, isLoggedIn } = storeToRefs(useUserStore());
</script>

<template>
  <main>
    <h1>Posts</h1>
    <section>
      <PostListComponent />
    </section>
  </main>
</template>

<style scoped>
h1 {
  text-align: center;
}
</style>
