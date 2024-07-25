<template>
  <div
    class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4"
  >
    <div v-for="photo in photos" :key="photo.id" class="relative">
      <img :src="photo.low_res_url" :alt="photo.title" class="w-full h-auto" />
      <div
        class="absolute bottom-0 left-0 right-0 bg-black bg-opacity-50 text-white p-2"
      >
        <p>{{ photo.title }}</p>
        <button
          @click="downloadPhoto(photo)"
          class="mt-2 px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
        >
          Download
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { useSupabaseClient } from '#imports';

const supabase = useSupabaseClient();

const props = defineProps({
  photos: {
    type: Array,
    required: true,
  },
});

const downloadPhoto = async (photo) => {
  try {
    const {
      data: { user },
      error: userError,
    } = await supabase.auth.getUser();

    if (userError) throw userError;

    if (user) {
      // User is logged in, download high-res photo
      window.open(photo.high_res_url, '_blank');
    } else {
      // User is not logged in, download low-res photo
      window.open(photo.low_res_url, '_blank');
    }

    // Update analytics
    await supabase.from('analytics').insert({
      photo_id: photo.id,
      action: 'download',
      user_id: user ? user.id : null,
    });
  } catch (error) {
    console.error('Error downloading photo:', error.message);
    // You might want to show an error message to the user here
  }
};
</script>
