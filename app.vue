<template>
  <div class="container">
    <video ref="video" class="video" autoplay></video>
    <div v-if="!state.hasCameraPermission" class="text-color-primary">
      Camera permission is required.
    </div>
    <button @click="capturePhoto" :disabled="!state.hasCameraPermission">Capture Photo</button>
    <div v-if="state.capturedPhoto">
      <h3>Captured Photo:</h3>
      <img :src="state.capturedPhoto" alt="Captured Photo"/>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, reactive } from 'vue';

const videoRef = ref(null);
const state = reactive({
  hasCameraPermission: false,
  type: 'user', // Set to 'user' to use the front camera
  capturedPhoto: null
});

const startCamera = async () => {
  try {
    const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: state.type } });
    videoRef.value.srcObject = stream;
    state.hasCameraPermission = true;
  } catch (err) {
    console.error('Error accessing the camera', err);
    state.hasCameraPermission = false;
  }
};

const capturePhoto = () => {
  const video = videoRef.value;
  if (video) {
    const canvas = document.createElement('canvas');
    canvas.width = video.videoWidth;
    canvas.height = video.videoHeight;
    const context = canvas.getContext('2d');
    context.drawImage(video, 0, 0, canvas.width, canvas.height);
    state.capturedPhoto = canvas.toDataURL('image/png');
  }
};

onMounted(() => {
  // Request camera permission when mounted
  navigator.mediaDevices.getUserMedia({ video: true })
    .then(() => {
      startCamera();
    })
    .catch((err) => {
      console.error('Error accessing the camera', err);
      state.hasCameraPermission = false;
    });
});
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.video {
  width: 100%;
  height: auto;
}

.text-color-primary {
  color: blue;
  margin-top: 10px;
}

button {
  background-color: brown;
  color: white;
  margin-top: 10px;
}
</style>
