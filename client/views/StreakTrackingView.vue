<script setup lang="ts">
import StreakListComponent from "@/components/Record/StreakListComponent.vue";
</script>

<template>
  <main>
    <h1>Habit Tracking</h1>
    <p>Keep track of good habits!</p>
    <StreakListComponent />
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
