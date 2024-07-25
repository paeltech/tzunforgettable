<template>
  <div>
    <h1>Welcome to Unsplash Clone</h1>
    <PhotoGrid :photos="photos" />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useSupabaseClient } from '#imports';
import PhotoGrid from '~/components/PhotoGrid.vue';

const supabase = useSupabaseClient();
const photos = ref([]);

const fetchPhotos = async () => {
  const { data, error } = await supabase
    .from('photos')
    .select('*')
    .order('created_at', { ascending: false });

  if (error) {
    console.error('Error fetching photos:', error);
  } else {
    photos.value = data;
  }
};

onMounted(fetchPhotos);
</script>
