<script setup lang="ts">
import LoginForm from "@/components/Login/LoginForm.vue";
import RegisterForm from "@/components/Login/RegisterForm.vue";
</script>

<template>
  <section class="login">
    <section class="page-section">
      <h1>Please login or register!</h1>
      <main class="row">
        <LoginForm />
        <RegisterForm />
      </main>
    </section>
  </section>
</template>

<style scoped>
.login {
  display: flex;
  flex-direction: column;
  align-items: center;
  max-height: 300px;
  margin-bottom: 55vh;
}

.page-section {
  padding: 8%;
}
</style>
