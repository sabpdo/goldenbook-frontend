<script setup lang="ts">
import { useUserStore } from "@/stores/user";
import { fetchy } from "@/utils/fetchy";
import { storeToRefs } from "pinia";
import { formatDate } from "@/utils/formatDate";

const props = defineProps(["record"]);
const emit = defineEmits(["editRecord", "refreshRecords"]);
const { currentUsername } = storeToRefs(useUserStore());

const deleteRecord = async () => {
  try {
    await fetchy(`/api/records/${props.record._id}`, "DELETE");
  } catch {
    return;
  }
  emit("refreshRecords");
};
</script>

<template>
  <p class="author">{{ props.record.receiver }}</p>
  <p>{{ props.record.content }}</p>
  <div class="base">
    <menu v-if="props.record.receiver == currentUsername">
      <li><button class="btn-small pure-button" @click="emit('editRecord', props.record._id)">Edit</button></li>
      <li><button class="button-error btn-small pure-button" @click="deleteRecord">Delete</button></li>
    </menu>
    <article class="timestamp">
      <p v-if="props.record.dateCreated !== props.record.dateUpdated">Edited on: {{ formatDate(props.record.dateUpdated) }}</p>
      <p v-else>Created on: {{ formatDate(props.record.dateCreated) }}</p>
    </article>
  </div>
</template>

<style scoped>
p {
  margin: 0em;
}
</style>
