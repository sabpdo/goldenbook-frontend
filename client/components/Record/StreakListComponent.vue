<script setup lang="ts">
import { useUserStore } from "@/stores/user";
import { fetchy } from "@/utils/fetchy";
import { storeToRefs } from "pinia";
import { onBeforeMount, ref } from "vue";
import CalendarActionComponent from "./CalendarActionComponent.vue";
import StartStreakForm from "./StartStreakForm.vue";

const { currentUsername, isLoggedIn } = storeToRefs(useUserStore());
const loaded = ref(false);
let records = ref<Array<Record<string, string>>>([]);
const groupedRecords = ref({});

async function getRecords(receiver?: string) {
  let query: Record<string, string> = receiver !== undefined ? { receiver } : {};
  let recordResults;
  try {
    recordResults = await fetchy("/api/records", "GET", { query });
    records.value = recordResults;
    groupRecordsByAction(recordResults);
  } catch (_) {
    return;
  }
}

const groupRecordsByAction = (records: Array<Record<string, any>>) => {
  groupedRecords.value = records.reduce((acc, record) => {
    if (!acc[record.action]) {
      acc[record.action] = [];
    }
    acc[record.action].push(record.time);
    return acc;
  }, {});
};

onBeforeMount(async () => {
  await getRecords(currentUsername.value);
  loaded.value = true;
});
</script>

<template>
  <section v-if="isLoggedIn">
    <section class="record-actions">
      <StartStreakForm @refreshRecords="getRecords" />
    </section>
    <section class="records-list" v-if="loaded">
      <div v-if="Object.keys(groupedRecords).length === 0">No records found</div>
      <div v-else>
        <CalendarActionComponent v-for="(records, action) in groupedRecords" :key="action" :action="action" :records="records" />
      </div>
    </section>
    <p v-else>Loading...</p>
  </section>
</template>

<style scoped></style>
