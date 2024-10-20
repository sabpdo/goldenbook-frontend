<script setup lang="ts">
import PostListComponent from "@/components/Post/PostListComponent.vue";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";
import CreatePostForm from "@/components/Post/CreatePostForm.vue";

const { currentUsername, isLoggedIn } = storeToRefs(useUserStore());
</script>

<template>
  <main>
    <h1>Community Board</h1>
    <p>Learn about what's happening in your community!</p>
    <section>
      <PostListComponent />
    </section>
  </main>
</template>

<style scoped>
h1 {
  text-align: center;
}
p {
  text-align: center;
  font-size: 1.25rem;
  font-style: italic;
}
</style>
