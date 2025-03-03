<!-- src/components/CameraCapture.vue -->
<template>
  <div>
    <!-- Placeholder for camera capture elements (button, video, etc.) -->
    <button @click="captureImage">Capture Image</button>
    <video ref="videoElement" autoplay></video>
    <canvas ref="canvasElement" style="display: none"></canvas>
  </div>
</template>
  
  <script>
export default {
  data() {
    return {
      stream: null, // To store the camera stream
    };
  },
  methods: {
    async captureImage() {
      const video = this.$refs.videoElement;
      const canvas = this.$refs.canvasElement;
      const context = canvas.getContext("2d");

      // Set canvas dimensions to match video dimensions
      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;

      // Draw the current video frame onto the canvas
      context.drawImage(video, 0, 0, canvas.width, canvas.height);

      // Get the image data as a base64-encoded string
      const imageData = canvas.toDataURL("image/png");

      // Emit the captured image data to the parent component
      this.$emit("capture", imageData);
    },
    async startCamera() {
      try {
        this.stream = await navigator.mediaDevices.getUserMedia({
          video: true,
        });
        this.$refs.videoElement.srcObject = this.stream;
      } catch (error) {
        console.error("Error accessing the camera:", error);
        // Handle the error appropriately, e.g., display an error message to the user
      }
    },
    stopCamera() {
      if (this.stream) {
        this.stream.getTracks().forEach((track) => track.stop());
        this.stream = null; // Clear the stream
      }
    },
  },
  mounted() {
    this.startCamera();
  },
  beforeUnmount() {
    // Stop the camera stream when the component is unmounted
    this.stopCamera();
  },
};
</script>
  
  <style scoped>
/* Add component-specific styles here, if needed */
</style>