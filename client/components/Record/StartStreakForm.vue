<script setup lang="ts">
import { fetchy } from "../../utils/fetchy";
import { defineEmits, ref, onMounted } from "vue";

const emit = defineEmits(["refreshRecords"]);
const isTrackingMessaging = ref(false);
const isTrackingPosting = ref(false);

const getTrackingStatus = async () => {
  try {
    const response = await fetchy("/api/records/automatic", "GET");
    isTrackingMessaging.value = response.isTrackingMessaging;
    isTrackingPosting.value = response.isTrackingPosting;
  } catch (_) {
    return;
  }
};

onMounted(async () => {
  await getTrackingStatus();
});

const startTrackingMessaging = async () => {
  try {
    await fetchy("/api/records/automatic/message", "POST");
  } catch (_) {
    return;
  }
  await getTrackingStatus();
  emit("refreshRecords");
};

const stopTrackingMessaging = async () => {
  try {
    await fetchy("/api/records/automatic/message", "DELETE");
  } catch (_) {
    return;
  }
  await getTrackingStatus();
  emit("refreshRecords");
};

const startTrackingPosting = async () => {
  try {
    isTrackingPosting.value = true;
    await fetchy("/api/records/automatic/post", "POST");
  } catch (_) {
    return;
  }
  await getTrackingStatus();
  emit("refreshRecords");
};

const stopTrackingPosting = async () => {
  try {
    await fetchy("/api/records/automatic/post", "DELETE");
  } catch (_) {
    return;
  }
  await getTrackingStatus();
  emit("refreshRecords");
};
</script>
<template>
  <section class="tracking-section">
    <div class="tracking-group">
      <button v-if="!isTrackingMessaging" type="button" class="tracking-button" @click="startTrackingMessaging">Start Message Tracking</button>
      <button v-else type="button" class="tracking-button active" @click="stopTrackingMessaging">Stop Message Tracking</button>
    </div>

    <div class="tracking-group">
      <button v-if="!isTrackingPosting" type="button" class="tracking-button" @click="startTrackingPosting">Start Post Tracking</button>
      <button v-else type="button" class="tracking-button active" @click="stopTrackingPosting">Stop Post Tracking</button>
    </div>
  </section>
</template>

<style scoped>
.tracking-section {
  display: flex;
}

.tracking-group {
  display: flex;
  gap: 0.5em;
  padding: 1em;
}

.tracking-button.active {
  background-color: var(--green);
  color: white;
}
</style>
