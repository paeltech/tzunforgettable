<template>
  <form @submit.prevent="register" class="max-w-sm mx-auto">
    <div class="mb-4">
      <label for="email" class="block mb-2">Email</label>
      <input
        id="email"
        v-model="email"
        type="email"
        required
        class="w-full px-3 py-2 border rounded"
      />
    </div>
    <div class="mb-4">
      <label for="password" class="block mb-2">Password</label>
      <input
        id="password"
        v-model="password"
        type="password"
        required
        class="w-full px-3 py-2 border rounded"
      />
    </div>
    <button
      type="submit"
      class="w-full px-4 py-2 text-white bg-blue-500 rounded hover:bg-blue-600"
    >
      Register
    </button>
    <p v-if="error" class="mt-4 text-red-500">{{ error }}</p>
  </form>
</template>

<script setup>
import { ref } from 'vue';
import { useSupabaseClient, useSupabaseUser } from '#imports';

const supabase = useSupabaseClient();
const email = ref('');
const password = ref('');
const error = ref(null);

const register = async () => {
  try {
    const { data, error: signUpError } = await supabase.auth.signUp({
      email: email.value,
      password: password.value,
    });
    if (signUpError) throw signUpError;
    alert(
      'Registration successful! Please check your email to confirm your account.'
    );
  } catch (e) {
    error.value = e.message;
  }
};
</script>
