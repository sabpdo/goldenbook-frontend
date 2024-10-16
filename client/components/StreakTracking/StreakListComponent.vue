<script setup lang="ts">
import { useUserStore } from "@/stores/user";
import { fetchy } from "@/utils/fetchy";
import { storeToRefs } from "pinia";
import { onBeforeMount, ref } from "vue";
import StreakComponent from "./StreakComponent.vue";

const { currentUsername, isLoggedIn } = storeToRefs(useUserStore());
const loaded = ref(false);
let records = ref<Array<Record<string, string>>>([]);

async function getRecords(receiver?: string) {
  let query: Record<string, string> = receiver !== undefined ? { receiver } : {};
  let recordResults;
  try {
    recordResults = await fetchy("/api/records", "GET", { query });
  } catch (_) {
    return;
  }
  records.value = recordResults;
}

onBeforeMount(async () => {
  await getRecords(currentUsername.value);
  loaded.value = true;
});
</script>

<template>
  <section v-if="isLoggedIn">
    <h2>Streak Tracking</h2>
    <section class="records" v-if="loaded && records.length !== 0">
      <article v-for="record in records" :key="record._id">
        <StreakComponent :record="record" @refreshRecords="getRecords" />
      </article>
    </section>
    <p v-else-if="loaded">No records found</p>
    <p v-else>Loading...</p>
  </section>
</template>

<style scoped></style>
