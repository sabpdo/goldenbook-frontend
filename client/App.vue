<script setup lang="ts">
import { useToastStore } from "@/stores/toast";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";
import { computed, onBeforeMount } from "vue";
import { RouterLink, RouterView, useRoute } from "vue-router";
import router from "./router";

const currentRoute = useRoute();
const currentRouteName = computed(() => currentRoute.name);
const userStore = useUserStore();
const { isLoggedIn } = storeToRefs(userStore);
const { toast } = storeToRefs(useToastStore());

// Make sure to update the session before mounting the app in case the user is already logged in
onBeforeMount(async () => {
  try {
    await userStore.updateSession();
  } catch {
    // User is not logged in
    await router.push({ name: "Login" });
  }
});
</script>

<template>
  <header>
    <nav>
      <div class="title">
        <img src="@/assets/images/logo.jpg" />
        <RouterLink :to="{ name: 'Home' }">
          <h1>GoldenBook</h1>
        </RouterLink>
      </div>
      <ul>
        <li v-if="isLoggedIn">
          <RouterLink :to="{ name: 'Habit Tracking' }" :class="{ underline: currentRouteName == 'Habit Tracking' }"> Habit Tracking </RouterLink>
          <RouterLink :to="{ name: 'Community Board' }" :class="{ underline: currentRouteName == 'Community Board' }"> Community Board </RouterLink>
          <RouterLink :to="{ name: 'Messages' }" :class="{ underline: currentRouteName == 'Messages' }"> Messages </RouterLink>
          <RouterLink :to="{ name: 'Settings' }" :class="{ underline: currentRouteName == 'Settings' }"> Settings </RouterLink>
          <RouterLink :to="{ name: 'Profile' }" :class="{ underline: currentRouteName == 'Profile' }"> Profile </RouterLink>
        </li>
        <li v-else>
          <RouterLink :to="{ name: 'Login' }" :class="{ underline: currentRouteName == 'Login' }"> Login/Register </RouterLink>
        </li>
      </ul>
    </nav>
    <article v-if="toast !== null" class="toast" :class="toast.style">
      <p>{{ toast.message }}</p>
    </article>
  </header>
  <RouterView />
</template>

<style scoped>
@import "./assets/toast.css";

nav {
  padding: 1em 2em;
  background-color: var(--white);
  display: flex;
  align-items: center;
  border-bottom: 1px solid black;
}

h1 {
  font-size: 2em;
  margin: 0;
}

.title {
  display: flex;
  align-items: center;
  gap: 0.5em;
}

img {
  height: 2em;
}

a {
  font-size: large;
  color: black;
  text-decoration: none;
  padding: 0.25em;
}

ul {
  list-style-type: none;
  margin-left: auto;
  display: flex;
  align-items: center;
  flex-direction: row;
  gap: 1em;
}
.underline {
  text-decoration: underline;
}
</style>
