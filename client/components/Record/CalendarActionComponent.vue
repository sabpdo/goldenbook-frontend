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
    const formattedDate = formatDateByDay(new Date(record as unknown as string));
    dateSet.add(formattedDate);
  });
  return dateSet;
});

const currentYear = new Date().getFullYear();
const currentMonth = new Date().getMonth();
const currentDay = new Date().getDate();
const totalDaysInMonth = new Date(currentYear, currentMonth + 1, 0).getDate();
const daysOfWeek = ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"];
</script>

<template>
  <div class="wrap-action-calendar">
    <div class="action-calendar">
      <h2>Tracked Action: {{ props.action }}</h2>
      <div class="day-labels">
        <div v-for="day in daysOfWeek" :key="day" class="day-label">{{ day }}</div>
      </div>
      <div class="calendar-grid">
        <div v-for="day in totalDaysInMonth" :key="day" :class="['calendar-day', { 'current-day': day === currentDay }]">
          <div class="day-number">{{ day }}</div>
          <div class="emoji">
            <span v-if="actionDates.has(`${currentYear}-${(currentMonth + 1).toString().padStart(2, '0')}-${day.toString().padStart(2, '0')}`)">🔥</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.wrap-action-calendar {
  padding: 1em;
}
.action-calendar {
  width: 315px;
  margin: 0 auto;
  background-color: var(--base-bg);
  padding: 1em;
  border-radius: 1em;
  border: 0.5px solid var(--nav-green);
  border-radius: 10px;
}

.day-labels {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 1em;
  margin-bottom: 1em;
  text-align: center;
  font-weight: bold;
  background-color: var(--base-bg);
}

.calendar-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 1em;
  background-color: var(--base-bg);
}

.calendar-day {
  border: 1px solid #ddd;
  padding: 0.5em;
  text-align: center;
  font-size: 0.9em;
  border-radius: 0.5em;
  background-color: var(--base-bg);
}

.current-day {
  border: 4px solid var(--nav-green);
  background-color: #e6f4f2;
  border-radius: 10px;
  font-weight: bold;
}

.day-number {
  font-weight: bold;
  margin-bottom: 0.25em;
}
</style>
