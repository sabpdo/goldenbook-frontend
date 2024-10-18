<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";

const props = defineProps(["nudge"]);
const { currentUsername } = storeToRefs(useUserStore());

const isVisible = ref(false);

const checkNudgeTime = () => {
  const currentTime = new Date().getHours();
  const nudgeTime = new Date(props.nudge.time).getHours();

  if (currentTime === nudgeTime && props.nudge.to === currentUsername) {
    isVisible.value = true;
  } else {
    isVisible.value = false;
  }
};

onMounted(() => {
  checkNudgeTime();
  const interval = setInterval(checkNudgeTime, 60000);

  onUnmounted(() => {
    clearInterval(interval);
  });
});
</script>

<template>
  <transition name="slide">
    <div v-if="isVisible" class="nudge-container">
      <p class="Nudge">{{ props.nudge.to }}</p>
      <p>{{ props.nudge.time }}</p>
      <div class="base">
        <p>Reminder to stay in touch with friends and message!</p>
      </div>
    </div>
  </transition>
</template>

<style scoped>
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
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  opacity: 0;
  transform: translateX(100%);
  transition:
    transform 0.4s ease,
    opacity 0.4s ease;
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
