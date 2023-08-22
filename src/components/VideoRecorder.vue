<script setup lang="ts">
import { ref, onBeforeUnmount, Ref } from "vue";
import Timer from "./Timer/Timer.vue";
const video = ref();
const videoStream = ref();
const mediaRecorder = ref();
const recordedChunks: Ref<Array<any>> = ref([]);
const videoBlob = ref();
const recording = ref(false);
const uploading = ref(false);
const uploadProgress = ref(0);

interface Props {
  timeout: number;
}

const props = withDefaults(defineProps<Props>(), {
  timeout: 30000,
});

async function startRecording() {
  try {
    videoStream.value = await navigator.mediaDevices.getUserMedia({
      video: true,
    });
    recordedChunks.value = [];
    video.value.srcObject = videoStream.value;
    mediaRecorder.value = new MediaRecorder(videoStream.value);

    mediaRecorder.value.ondataavailable = (event: any) => {
      if (event.data.size > 0) {
        recordedChunks.value.push(event.data);
      }
    };

    mediaRecorder.value.onstop = () => {
      videoBlob.value = new Blob(recordedChunks.value, { type: "video/webm" });
      URL.createObjectURL(videoBlob.value);
    };

    mediaRecorder.value.start();
    recording.value = true;

    setTimeout(() => {
      stopRecording();
    }, props.timeout); // Stop recording after timeout
  } catch (error) {
    console.error("Error starting recording:", error);
  }
}

function stopRecording() {
  if (mediaRecorder.value && recording.value) {
    mediaRecorder.value.stop();
    videoStream.value.getTracks().forEach((track: any) => track.stop());
    recording.value = false;
  }
}

async function uploadVideo() {
  if (videoBlob.value) {
    // Simulate uploading process
    uploading.value = true;
    for (let progress = 0; progress <= 100; progress += 10) {
      uploadProgress.value = progress;
      await new Promise((resolve) => setTimeout(resolve, 500));
    }
    uploading.value = false;
    uploadProgress.value = 0;

    // Here, we implement actual uploading logic to the server
  }
}

// Clean up when the component is unmounted
onBeforeUnmount(() => {
  if (videoStream.value) {
    videoStream.value.getTracks().forEach((track: any) => track.stop());
  }
});
</script>

<template>
  <h2>Video Recorder</h2>
  <Timer v-if="recording" :time="timeout/1000"/>
  <video ref="video" autoplay></video>
  <div class="card">
    <button @click="startRecording" :disabled="recording">Record</button>
    <button @click="stopRecording" :disabled="!recording">Stop</button>
    <button @click="uploadVideo" :disabled="!videoBlob">
      Upload Recorded File
    </button>
  </div>
  <progress v-if="uploading" :value="uploadProgress" max="100"></progress>
</template>
