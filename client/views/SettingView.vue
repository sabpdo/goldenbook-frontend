<script setup lang="ts">
import router from "@/router";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";
import UpdateUserForm from "../components/Setting/UpdateUserForm.vue";
import CreateSocialWellnessNotification from "../components/Nudge/CreateSocialWellnessNotification.vue";
import AuthorizationComponentList from "@/components/Authorization/AuthorizationComponentList.vue";

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
  <section class="settings">
    <section class="setting-page">
      <h1>Settings for @{{ currentUsername }}</h1>

      <div class="setting-subsection">
        <h2>Account Actions</h2>
        <div class="button-group">
          <button @click="logout">Logout</button>
          <button class="button-error pure-button" @click="delete_">Delete User</button>
        </div>
      </div>

      <div class="setting-subsection">
        <h2>Your Social Wellness Notifications</h2>
        <p class="description">Remind me to stay connected with friends and family!</p>
        <CreateSocialWellnessNotification />
      </div>

      <div class="setting-subsection">
        <h2>Update Your Information</h2>
        <UpdateUserForm />
      </div>

      <div class="setting-subsection">
        <h2>Manage Your Authorizations</h2>
        <p class="description">View and edit who has control over your account.</p>
        <AuthorizationComponentList />
      </div>
    </section>
  </section>
</template>

<style scoped>
.settings {
  padding-top: 50px;
  padding-bottom: 120px;
}

.setting-page {
  display: flex;
  flex-direction: column;
  gap: 2rem;
  max-width: 90vh;
  margin: 0 auto;
  padding: 2rem;
  background-color: rgba(255, 255, 255, 0.85);
  border-radius: 8px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  text-align: center;
  border: 0.5px solid black;
}

.setting-subsection {
  padding: 1.5rem;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
  text-align: left;
  border-radius: 10px;
  box-shadow: 0px 4px 8px var(--green);
  margin-bottom: 30px;
  border: 1.5px solid var(--green);
}

h1,
h2 {
  text-align: center;
}

.description {
  font-style: italic;
  margin-bottom: 1rem;
  text-align: center;
}

.button-group {
  display: flex;
  justify-content: center;
  gap: 1rem;
  margin-top: 1rem;
}

button {
  padding: 0.5rem 1rem;
  border-radius: 0.25em;
  cursor: pointer;
}

.button-error {
  background-color: #e63946;
  color: white;
}

button-error:hover {
  background-color: #d62828;
}
</style>
