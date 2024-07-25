<template>
  <div>
    <nav class="bg-gray-800 text-white p-4">
      <div class="container mx-auto flex justify-between items-center">
        <NuxtLink to="/" class="text-xl font-bold">Unsplash Clone</NuxtLink>
        <div v-if="user">
          <span class="mr-4">{{ user.email }}</span>
          <button
            @click="logout"
            class="bg-red-500 px-4 py-2 rounded hover:bg-red-600"
          >
            Logout
          </button>
        </div>
        <NuxtLink
          v-else
          to="/auth"
          class="bg-blue-500 px-4 py-2 rounded hover:bg-blue-600"
          >Login/Register</NuxtLink
        >
      </div>
    </nav>
    <main class="container mx-auto mt-8">
      <slot />
    </main>
  </div>
</template>

<script setup>
import { useSupabaseClient, useSupabaseUser } from '#imports';
import { useRouter } from 'vue-router';

const supabase = useSupabaseClient();
const user = useSupabaseUser();
const router = useRouter();

const logout = async () => {
  await supabase.auth.signOut();
  router.push('/auth');
};
</script>
