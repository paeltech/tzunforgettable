<template>
  <form @submit.prevent="login" class="max-w-sm mx-auto">
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
      class="w-full px-4 py-2 text-white bg-green-500 rounded hover:bg-green-600"
    >
      Login
    </button>
    <p v-if="error" class="mt-4 text-red-500">{{ error }}</p>
  </form>
</template>

<script setup>
import { ref } from 'vue';
import { useSupabaseClient, useSupabaseUser } from '#imports';
import { useRouter } from 'vue-router';

const supabase = useSupabaseClient();
const user = useSupabaseUser();
const router = useRouter();

const email = ref('');
const password = ref('');
const error = ref(null);

const login = async () => {
  try {
    const { data, error: signInError } = await supabase.auth.signInWithPassword(
      {
        email: email.value,
        password: password.value,
      }
    );
    if (signInError) throw signInError;
    router.push('/'); // Redirect to home page after successful login
  } catch (e) {
    error.value = e.message;
  }
};
</script>
