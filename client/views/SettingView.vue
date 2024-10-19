<script setup lang="ts">
import router from "@/router";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";
import UpdateUserForm from "../components/Setting/UpdateUserForm.vue";

const { currentUsername } = storeToRefs(useUserStore());
const { logoutUser, deleteUser } = useUserStore();

async function logout() {
  await logoutUser();
  void router.push({ name: "Home" });
}

async function delete_() {
  await deleteUser();
  void router.push({ name: "Home" });
}
</script>

<template>
  <section class="wrapper">
  <section class="setting-page">
  <main class="column">
    <h1>Settings for @{{ currentUsername }}</h1>
    <button @click="logout">Logout</button>
    <button class="button-error pure-button" @click="delete_">Delete User</button>
    <UpdateUserForm />
  </main>
</section>
</section>
</template>

<style scoped>
button {
  align-self: center;
  border-radius: 0.25em;
}
.setting-page {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 70vh;
  background-color: rgba(255, 255, 255, 0.85);
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  max-width: 600px;
  margin: 0 auto;
  border-radius: 8px;
  text-align: center;
}
.wrapper {
  padding-top: 50px;
  padding-bottom: 120px;
}
</style>
