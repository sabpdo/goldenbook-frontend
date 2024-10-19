<script setup lang="ts">
import NudgeComponent from "./NudgeComponent.vue";
import { useUserStore } from "@/stores/user";
import { fetchy } from "@/utils/fetchy";
import { storeToRefs } from "pinia";
import { onBeforeMount, ref } from "vue";

const { isLoggedIn, currentUsername } = storeToRefs(useUserStore());
const loaded = ref(false);
let nudges = ref<Array<Record<string, string>>>([]);
const emit = defineEmits(["toUser", "refreshNudges"]);

async function getNudges() {
  let query: Record<string, string> = { receiver: currentUsername.value };
  let nudgeResults;
  try {
    nudgeResults = await fetchy("/api/nudges", "GET", { query });
  } catch (_) {
    return;
  }
  nudges.value = nudgeResults;
}

onBeforeMount(async () => {
  await getNudges();
  loaded.value = true;
});
</script>

<template>
  <section v-if="isLoggedIn && loaded && nudges.length !== 0" class="nudge-container">
    <article v-for="(nudge, index) in nudges" :key="nudge._id" :style="{ top: `${index * 80}px` }" class="nudge-wrapper">
      <NudgeComponent :nudge="nudge" />
    </article>
  </section>
</template>

<style scoped>
.nudge-container {
  position: fixed;
  right: 1em;
  top: 1em;
  width: 20em;
  display: flex;
  flex-direction: column;
  gap: 0.5em;
}

.nudge-wrapper {
  position: absolute;
  right: 0;
  width: 100%;
  transition: top 0.4s ease;
}
</style>
