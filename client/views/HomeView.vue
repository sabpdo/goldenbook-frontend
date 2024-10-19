<script setup lang="ts">
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";
const { currentUsername, isLoggedIn } = storeToRefs(useUserStore());
</script>

<template>
  <main class="landing-page">
    <section class="page-section" v-if="isLoggedIn">
      <h1>Welcome {{ currentUsername }}!</h1>
      <p class="hero-subtitle"><router-link class="link-button" :to="{ name: 'Profile' }">Enter here!</router-link></p>
    </section>
    <section class="page-section" v-else>
      <h1>
        Welcome to GoldenBook!
        <p class="hero-subtitle">
          Please
          <router-link class="link-button" :to="{ name: 'Login' }">login</router-link>
          or
          <router-link class="link-button" :to="{ name: 'Login' }">register</router-link>
          to continue.
        </p>
      </h1>
    </section>
  </main>
</template>
<style scoped>
.landing-page {
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: linear-gradient(135deg, #e0f7fa, #80deea);
}

.hero-subtitle {
  font-size: 1.2rem;
  color: var(--nav-green);
  margin-top: 10px;
}

.welcome-message {
  font-size: 2rem;
  color: var(--nav-green);
}

.link-button {
  color: #00796b;
  font-weight: 500;
  text-decoration: none;
  padding: 10px 20px;
  border: 2px solid #00796b;
  border-radius: 20px;
  transition: all 0.3s ease;
  margin: 0 5px;
}

.link-button:hover {
  background-color: #00796b;
  color: white;
}
</style>
