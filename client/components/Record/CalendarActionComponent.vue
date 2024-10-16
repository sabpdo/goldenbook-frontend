<script setup lang="ts">
import { computed } from "vue";
import { formatDateByDay } from "@/utils/formatDate";

const props = defineProps<{
  action: string;
  records: Array<Record<string, string>>;
}>();

const actionDates = computed(() => {
  const dateSet = new Set<string>();
  props.records.forEach((record) => {
    console.log(record);
    const formattedDate = formatDateByDay(new Date(record));
    dateSet.add(formattedDate);
  });
  console.log(dateSet);
  return dateSet;
});

const currentYear = new Date().getFullYear();
const currentMonth = new Date().getMonth();
const totalDaysInMonth = new Date(currentYear, currentMonth + 1, 0).getDate();
</script>

<template>
  <div class="action-calendar">
    <h2>{{ props.action }}</h2>
    <div class="calendar-grid">
      <div v-for="day in totalDaysInMonth" :key="day" class="calendar-day">
        <div class="day-number">{{ day }}</div>
        <div class="emoji">
          <span v-if="actionDates.has(`${currentYear}-${(currentMonth + 1).toString().padStart(2, '0')}-${day.toString().padStart(2, '0')}`)">🔥</span>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.calendar-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 1em;
}
.calendar-day {
  border: 1px solid #ddd;
  padding: 0.5em;
  text-align: center;
}
.day-number {
  font-weight: bold;
}
</style>
