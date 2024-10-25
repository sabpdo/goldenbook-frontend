<script setup lang="ts">
import { fetchy } from "../../utils/fetchy";
import CurrentAllowedActionList from "./CurrentAllowedActionList.vue";
import AuthorizationActionForm from "@/components/Authorization/AuthorizationActionForm.vue";
import ControlAuthorizersList from "@/components/Authorization/ControlAuthorizersList.vue";
import { ref, onBeforeMount } from "vue";
import { useUserStore } from "@/stores/user";
import { storeToRefs } from "pinia";

const authorizees = ref([]);
const authorizers = ref([]);
const loaded = ref(false);
const { isLoggedIn } = storeToRefs(useUserStore());

const getAuthorizations = async () => {
  try {
    const response = await fetchy("/api/authorize/control", "GET");
    console.log(response);
    authorizees.value = response.authorizees;
    authorizers.value = response.authorizers;
  } catch {
    return;
  }
};

onBeforeMount(async () => {
  await getAuthorizations();
  loaded.value = true;
});
</script>

<template>
  <section v-if="loaded && isLoggedIn">
    <CurrentAllowedActionList />
    <div v-if="authorizees.length !== 0" class="container">
      <section v-for="authorizee in authorizees" :key="authorizee" class="authorizees-container">
        <AuthorizationActionForm :authorizee="authorizee" />
      </section>
    </div>
    <div v-else>
      <p>You do not have authorization control over any acccounts.</p>
    </div>
    <ControlAuthorizersList :authorizers="authorizers" @refreshAuthorizers="getAuthorizations" />
  </section>
</template>

<style scoped>
h3 {
  text-align: center;
  color: var(--nav-green);
  margin-bottom: 20px;
}
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 10px;
  box-shadow: 0px 4px 8px var(--light-green);
  margin-bottom: 30px;
  border: 1px solid var(--light-green);
}
</style>
