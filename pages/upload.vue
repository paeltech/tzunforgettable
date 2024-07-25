<template>
  <div class="container mx-auto mt-8">
    <h1 class="text-2xl font-bold mb-4">Upload Photo</h1>
    <form @submit.prevent="uploadPhoto" class="max-w-md mx-auto">
      <div class="mb-4">
        <label for="file" class="block mb-2">Choose Photo</label>
        <input
          id="file"
          type="file"
          @change="handleFileChange"
          accept="image/*"
          required
          class="w-full px-3 py-2 border rounded"
        />
      </div>
      <div class="mb-4">
        <label for="title" class="block mb-2">Photo Title</label>
        <input
          id="title"
          v-model="title"
          type="text"
          required
          class="w-full px-3 py-2 border rounded"
          placeholder="Enter photo title"
        />
      </div>
      <button
        type="submit"
        class="w-full px-4 py-2 text-white bg-blue-500 rounded hover:bg-blue-600"
      >
        Upload
      </button>
    </form>
    <p v-if="error" class="mt-4 text-red-500">{{ error }}</p>
    <p v-if="successMessage" class="mt-4 text-green-500">
      {{ successMessage }}
    </p>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { useSupabaseClient } from '#imports';

const supabase = useSupabaseClient();
const title = ref('');
const file = ref(null);
const error = ref(null);
const successMessage = ref(null);

const handleFileChange = (event) => {
  file.value = event.target.files[0];
};

const uploadPhoto = async () => {
  error.value = null;
  successMessage.value = null;

  try {
    // Get the current user
    const {
      data: { user },
      error: userError,
    } = await supabase.auth.getUser();
    if (userError) throw userError;
    if (!user) throw new Error('You must be logged in to upload photos');

    // Check if a file is selected
    if (!file.value) throw new Error('Please select a file to upload');

    // Generate a unique file name
    const fileExt = file.value.name.split('.').pop();
    const fileName = `${Date.now()}.${fileExt}`;
    const filePath = `${user.id}/${fileName}`;

    // Upload the file to Supabase Storage
    const { data, error: uploadError } = await supabase.storage
      .from('images')
      .upload(filePath, file.value);

    if (uploadError) throw uploadError;

    // Get the public URL of the uploaded file
    const {
      data: { publicUrl },
      error: urlError,
    } = supabase.storage.from('images').getPublicUrl(filePath);

    if (urlError) throw urlError;

    console.log('Public URL:', publicUrl); // Log the URL for debugging

    // Insert the photo information into the database
    const { data: photoData, error: insertError } = await supabase
      .from('photos')
      .insert({
        user_id: user.id,
        title: title.value,
        high_res_url: publicUrl,
        low_res_url: publicUrl, // For simplicity, using the same URL for both.
      });

    if (insertError) throw insertError;

    successMessage.value = 'Photo uploaded successfully!';
    title.value = '';
    file.value = null;
  } catch (e) {
    console.error('Upload error:', e); // Log the full error for debugging
    error.value = e.message;
  }
};
</script>
