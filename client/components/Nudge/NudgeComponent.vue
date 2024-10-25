<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";

const props = defineProps(["nudge"]);
const { currentUsername } = storeToRefs(useUserStore());
const formattedTime = ref("");
const emit = defineEmits(["refreshNudges"]);

const isVisible = ref(false);

const checkNudgeTime = () => {
  const currentTime = new Date().getTime();
  const nudgeTime = new Date(props.nudge.time).getTime();
  console.log(new Date(currentTime), new Date(nudgeTime));

  if (currentTime >= nudgeTime && currentTime <= nudgeTime + 600000 && props.nudge.to === currentUsername.value) {
    isVisible.value = true;
    const nudgeDate = new Date(props.nudge.time);
    formattedTime.value = new Intl.DateTimeFormat("en", {
      dateStyle: "medium",
      timeStyle: "short",
    }).format(nudgeDate);
  }
};

const closeNudge = () => {
  isVisible.value = false;
  emit("refreshNudges");
};

onMounted(() => {
  const interval = setInterval(() => {
    checkNudgeTime();
    emit("refreshNudges");
  }, 60000);

  onUnmounted(() => {
    clearInterval(interval);
  });
});
</script>

<template>
  <transition name="slide">
    <div v-if="isVisible" class="nudge-container">
      <button @click="closeNudge" class="close">✕</button>
      <p class="nudge-header">👋 You have a nudge from {{ props.nudge.from }}!</p>
      <p class="nudge-time">Received at: {{ formattedTime }}</p>
      <div class="nudge-message">
        <p>Just a friendly reminder to check in and message your friends!</p>
      </div>
    </div>
  </transition>
</template>

<style scoped>
.close {
  border-color: var(--red);
  font-size: 1.2em;
  position: absolute;
  top: 0.5em;
  right: 0.5em;
  cursor: pointer;
  color: var(--red);
  padding: 0.5em;
}
.close:hover {
  color: darkred;
  background-color: var(--base-bg);
}
.nudge-container {
  background-color: var(--base-bg);
  border-radius: 2em;
  display: flex;
  flex-direction: column;
  gap: 0.5em;
  padding: 1em;
  width: 15em;
  position: fixed;
  right: 1em;
  top: 1em;
  opacity: 1;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  opacity: 0;
  transform: translateX(100%);
  transition:
    transform 10000s ease,
    opacity 10000s ease;
}

.nudge-container .base {
  color: #333;
}
.slide-enter-active,
.slide-leave-active {
  transition:
    transform 0.4s ease,
    opacity 0.4s ease;
}

.slide-enter-from {
  opacity: 0;
  transform: translateX(100%);
}

.slide-enter-to {
  opacity: 1;
  transform: translateX(0);
}

.slide-leave-from {
  opacity: 1;
  transform: translateX(0);
}

.slide-leave-to {
  opacity: 0;
  transform: translateX(100%);
}
</style>
