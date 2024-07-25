<template>
  <div>
    <h1>Your Photo Analytics</h1>
    <div v-for="photo in photos" :key="photo.id">
      <img :src="photo.low_res_url" :alt="photo.title" class="w-32 h-auto" />
      <p>{{ photo.title }}</p>
      <p>Views: {{ photo.views }}</p>
      <p>Downloads: {{ photo.downloads }}</p>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  setup() {
    const photos = ref([]);

    const fetchAnalytics = async () => {
      const user = useSupabaseUser();
      if (!user.value) return;

      const { data, error } = await useSupabaseClient()
        .from('photos')
        .select(
          `
        *,
        analytics:analytics(count)
      `
        )
        .eq('user_id', user.value.id);

      if (data) {
        photos.value = data.map((photo) => ({
          ...photo,
          views: photo.analytics.filter((a) => a.action === 'view').length,
          downloads: photo.analytics.filter((a) => a.action === 'download')
            .length,
        }));
      }
    };

    onMounted(fetchAnalytics);

    return { photos };
  },
};
</script>
