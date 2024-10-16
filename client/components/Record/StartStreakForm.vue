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
    await getTrackingStatus();
  } catch (_) {
    return;
  }
  emit("refreshRecords");
};

const stopTrackingMessaging = async () => {
  try {
    await fetchy("/api/records/automatic/message", "DELETE");
    await getTrackingStatus();
  } catch (_) {
    return;
  }
  emit("refreshRecords");
};

const startTrackingPosting = async () => {
  try {
    await fetchy("/api/records/automatic/post", "POST");
    await getTrackingStatus();
  } catch (_) {
    return;
  }
  emit("refreshRecords");
};

const stopTrackingPosting = async () => {
  try {
    await fetchy("/api/records/automatic/message", "DELETE");
    await getTrackingStatus();
  } catch (_) {
    return;
  }
  emit("refreshRecords");
};
</script>

<template>
  <section>
    <button v-if="!isTrackingMessaging" type="button" class="pure-button-primary pure-button" @click="startTrackingMessaging">Start Messaging</button>
    <button v-else type="button" class="pure-button-primary pure-button" @click="stopTrackingMessaging">Stop Messaging</button>
  </section>

  <section>
    <button v-if="!isTrackingPosting" type="button" class="pure-button-primary pure-button" @click="startTrackingPosting">Start Posting</button>
    <button v-else type="button" class="pure-button-primary pure-button" @click="stopTrackingPosting">Stop Posting</button>
  </section>
</template>

<style scoped>
form {
  background-color: var(--base-bg);
  border-radius: 1em;
  display: flex;
  flex-direction: column;
  gap: 0.5em;
  padding: 1em;
}
</style>
