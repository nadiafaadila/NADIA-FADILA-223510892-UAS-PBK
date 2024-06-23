<template>
  <q-page class="photo-details-page q-pa-md">
    <q-spinner v-if="isLoading" size="lg" color="primary" />
    <q-card v-else-if="photo" class="photo-card q-pa-md">
      <q-img :src="photo.url" class="photo-image q-mb-md" :alt="photo.title" />
      <div class="photo-title">{{ photo.title }}</div>
      <q-btn @click="goBack" color="primary" class="back-btn q-mt-md">
        <span class="btn-text">Back to Gallery</span>
        <span class="btn-gradient"></span>
      </q-btn>
    </q-card>
  </q-page>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { useRoute, useRouter } from "vue-router";

const route = useRoute();
const router = useRouter();
const photo = ref(null);
const isLoading = ref(false);

const fetchPhoto = async (id) => {
  try {
    isLoading.value = true;
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/photos/${id}`
    );
    if (!response.ok) throw new Error(`Error: ${response.statusText}`);
    photo.value = await response.json();
  } catch (error) {
    console.error("Error fetching photo:", error);
  } finally {
    isLoading.value = false;
  }
};

const goBack = () => {
  router.push("/album");
};

onMounted(() => {
  fetchPhoto(route.params.photoId);
});
</script>

<style scoped>
.q-page {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  background-color: #eea2a2; /* Background color for the page */
  min-height: 100vh; /* Ensure full viewport height */
}

.photo-card {
  max-width: 400px; /* Limit width of the card */
  background-color: #ffffff; /* Card background color */
  border-radius: 12px; /* Rounded corners for the card */
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1); /* Shadow for the card */
  padding: 30px; /* Padding inside the card */
  text-align: center; /* Center align text content */
}

.photo-image {
  max-width: 100%; /* Ensure image fits within the card */
  border-radius: 8px; /* Rounded corners for the image */
}

.photo-title {
  font-size: 1.5rem; /* Larger font size for title */
  font-weight: bold; /* Bold font weight for emphasis */
  margin-bottom: 15px; /* Space below the title */
}

.back-btn {
  margin-top: 20px; /* Space above the button */
  width: 200px; /* Set button width */
  font-size: 1rem; /* Font size for button text */
  position: relative; /* Position relative for gradient overlay */
  overflow: hidden; /* Ensure gradient does not overflow */
}

.btn-text {
  position: relative; /* Ensure text is in the foreground */
  z-index: 2; /* Text above gradient */
}

.btn-gradient {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, #ff9a9e, #fecfef); /* Gradient colors */
  transition: opacity 0.3s ease; /* Smooth opacity transition */
  z-index: 1; /* Gradient below text */
  opacity: 0; /* Initially hidden */
}

.back-btn:hover .btn-gradient {
  opacity: 1; /* Show gradient on hover */
}
</style>
