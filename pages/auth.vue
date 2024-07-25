<template>
  <div class="container mx-auto mt-8">
    <div class="flex justify-center space-x-8">
      <div class="w-1/2">
        <h2 class="text-2xl font-bold mb-4">Register</h2>
        <Register />
      </div>
      <div class="w-1/2">
        <h2 class="text-2xl font-bold mb-4">Login</h2>
        <Login />
      </div>
    </div>
  </div>
</template>

<script setup>
import Register from '~/components/Register.vue';
import Login from '~/components/Login.vue';
</script>
