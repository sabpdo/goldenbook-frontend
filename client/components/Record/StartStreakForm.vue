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
    isTrackingMessaging.value = true;
    await fetchy("/api/records/automatic/message", "POST");
    await getTrackingStatus();
  } catch (_) {
    isTrackingMessaging.value = false;
    return;
  }
  emit("refreshRecords");
};

const stopTrackingMessaging = async () => {
  try {
    isTrackingMessaging.value = false;
    await fetchy("/api/records/automatic/message", "DELETE");
    await getTrackingStatus();
  } catch (_) {
    isTrackingMessaging.value = true;
    return;
  }
  emit("refreshRecords");
};

const startTrackingPosting = async () => {
  try {
    isTrackingPosting.value = true;
    await fetchy("/api/records/automatic/post", "POST");
    await getTrackingStatus();
  } catch (_) {
    isTrackingPosting.value = false;
    return;
  }
  emit("refreshRecords");
};

const stopTrackingPosting = async () => {
  try {
    isTrackingPosting.value = false;
    await fetchy("/api/records/automatic/post", "DELETE");
    await getTrackingStatus();
  } catch (_) {
    isTrackingPosting.value = true;
    return;
  }
  emit("refreshRecords");
};
</script>
<template>
  <section class="tracking-section">
    <div class="tracking-group">
      <button v-if="!isTrackingMessaging" type="button" class="tracking-button" @click="startTrackingMessaging">Start Messaging</button>
      <button v-else type="button" class="tracking-button active" @click="stopTrackingMessaging">Stop Messaging</button>
    </div>

    <div class="tracking-group">
      <button v-if="!isTrackingPosting" type="button" class="tracking-button" @click="startTrackingPosting">Start Posting</button>
      <button v-else type="button" class="tracking-button active" @click="stopTrackingPosting">Stop Posting</button>
    </div>
  </section>
</template>

<style scoped>
.tracking-section {
  display: flex;
  justify-content: left;
  padding: 1em;
  border-radius: 1em;
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
}

.tracking-group {
  display: flex;
  gap: 1em;
  padding: 0.5em;
}

.tracking-button {
  padding: 0.75em 1.5em;
  background-color: var(--base-bg);
  color: black;
  border: none;
  border-radius: 0.5em;
  font-size: 1.1em;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.tracking-button:hover {
  background-color: darkgrey;
}

.tracking-button.active {
  background-color: dark grey;
}
</style>
