// src/App.vue
<template>
  <div class="container">
    <h1>Image Recognition</h1>
    <CameraCapture @capture="handleCapture" />

    <p v-if="error" class="error-message">Error: {{ error }}</p>
    <img v-if="imageUrl" :src="imageUrl" alt="Captured image" />

    <div class="results-section">
      <h2>Detections:</h2>
      <ul>
        <li v-for="(detection, index) in detections" :key="index">
          {{ detection.class_name }} (Confidence:
          {{ detection.confidence.toFixed(2) }})
          <div class="bounding-box-container">
            {/* You would add bounding box rendering here later */}
          </div>
        </li>
      </ul>

      <h2>Ranking:</h2>
      <ul>
        <li v-for="(count, objectName) in stats" :key="objectName">
          {{ objectName }}: {{ count }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import CameraCapture from "./components/CameraCapture.vue";
import { sendImageToBackend, fetchStats } from "./services/api"; // Corrected import path

export default {
  components: {
    CameraCapture,
  },
  data() {
    return {
      detections: [],
      stats: {},
      error: null,
      imageUrl: null,
    };
  },
  methods: {
    async handleCapture(imageData) {
      this.imageUrl = imageData;
      this.error = null;
      this.detections = [];

      try {
        const results = await sendImageToBackend(imageData);
        this.detections = results;
        this.fetchStatsData(); // Fetch updated stats
      } catch (err) {
        this.error = err.message;
      }
    },
    async fetchStatsData() {
      try {
        const fetchedStats = await fetchStats();
        this.stats = fetchedStats;
      } catch (err) {
        this.error = err.message;
      }
    },
  },
  mounted() {
    this.fetchStatsData(); // Fetch stats when the component is mounted
  },
};
</script>

<style>
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  text-align: center;
}

.error-message {
  color: red;
  margin-top: 10px;
}

.results-section {
  margin-top: 20px;
}

/* Add more styles as needed */
</style>