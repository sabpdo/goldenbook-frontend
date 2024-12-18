<script setup lang="ts">
import { useUserStore } from "@/stores/user";
import { fetchy } from "@/utils/fetchy";
import { storeToRefs } from "pinia";
import { onBeforeMount, ref } from "vue";
import CalendarActionComponent from "./CalendarActionComponent.vue";
import StartStreakForm from "./StartStreakForm.vue";
import CreateRecordForm from "./CreateRecordForm.vue";

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
  } catch {
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
      <StartStreakForm @refreshRecords="getRecords(currentUsername)" />
      <CreateRecordForm @refreshRecords="getRecords(currentUsername)" />
    </section>
    <section class="records-list" v-if="loaded">
      <p v-if="Object.keys(groupedRecords).length === 0" class="no-records">...No records found! Record your first habit!</p>
      <div v-else class="grouped-records">
        <CalendarActionComponent v-for="(records, action) in groupedRecords" :key="action" :action="action" :records="records" />
      </div>
    </section>
    <p v-else>Loading...</p>
  </section>
</template>

<style scoped>
.records-list {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 40vh;
}

.calendar {
  display: flex;
  flex-direction: column;
}

section {
  padding: 16px;
  font-family: Arial, sans-serif;
}

.record-actions {
  display: flex;
  gap: 1em;
  margin-bottom: 10px;
  justify-content: center;
}

p {
  color: var(--nav-green);
  font-family: "sans-serif", "Trebuchet MS";
  text-align: center;
  font-weight: bold;
  font-style: italic;
  font-size: 20px;
  padding: 20px;
  display: flex;
  justify-content: center;
}

.grouped-records {
  display: flex;
  flex-wrap: wrap;
  grid-template-columns: repeat(3, 1fr);
  gap: 1em;
}

.grouped-records > * {
  align-self: start;
}

.loading {
  text-align: center;
  font-size: 16px;
  color: #888;
}
</style>
