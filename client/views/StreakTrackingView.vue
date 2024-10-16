<script setup lang="ts">
import StreakListComponent from "@/components/Record/StreakListComponent.vue";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";

const { currentUsername, isLoggedIn } = storeToRefs(useUserStore());
</script>

<template>
  <main>
    <h1>Streak Tracking</h1>
    <StreakListComponent />
  </main>
</template>

<style scoped>
h1 {
  text-align: center;
}
</style>
