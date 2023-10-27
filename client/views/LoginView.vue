<script setup lang="ts">
import LoginForm from "@/components/Login/LoginForm.vue";
import RegisterForm from "@/components/Login/RegisterForm.vue";
</script>

<template>
  <main class="text content column">
    <div class="col-md-6 column" style="color: var(--gray)">
      <h1 class="shimmer-text pulse-heading">pulse</h1>
    </div>
    <div class="login column">
      <LoginForm />
      <RegisterForm />
    </div>
  </main>
</template>

<style scoped>

.text {
  font-family: mandali;
}

.content {
  display: flex;
  justify-content: center;
}

.pulse-heading {
  margin-top: 150px;
  margin-bottom: 10px;
  font-size: 100px;
  font-weight: normal;
}

.login{
  display: flex;
  margin-right: 100px;
  margin-bottom: 100px;
  margin-top: 10px;
}

main {
  height: 100vh;
  background: linear-gradient(90deg,rgb(251, 222, 222), rgba(213, 208, 208, 0.9) 50%, rgba(251, 222, 222, 0.9));
}
</style>





