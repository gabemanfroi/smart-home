<template>
  <div class="container">
    <h1>🌱 Bonsai Soil Moisture Tracker</h1>
    <p v-if="!connected" class="error">⚠️ Not Connected to WebSocket</p>
    <div v-if="sensorData.length > 0">
      <div v-for="data in sensorData" :key="data.sensor_id" class="sensor-card">
        <h3>{{ getSensorName(data.sensor_id) }}</h3>
        <p>Moisture Level: <strong>{{ data.moisture_level }}%</strong></p>
      </div>
    </div>
    <p v-else>No data received yet...</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      socket: null,
      sensorData: [],
      connected: false,
      sensorNames: {
        "1": "勝", // Sensor 1 (Kanji for "Victory")
        "2": "Buxus" // Sensor 2 (Buxus Harlandii)
      }
    };
  },
  mounted() {
    this.connectWebSocket();
  },
  methods: {
    connectWebSocket() {
      this.socket = new WebSocket("ws://192.168.3.155:8080");

      this.socket.onopen = () => {
        console.log("✅ WebSocket Connected!");
        this.connected = true;
      };

      this.socket.onmessage = (event) => {
        const data = JSON.parse(event.data);
        console.log("📡 Received data:", data);

        // Replace old data if sensor_id already exists
        const index = this.sensorData.findIndex((s) => s.sensor_id === data.sensor_id);
        if (index !== -1) {
          this.sensorData[index] = data;
        } else {
          this.sensorData.push(data);
        }
      };

      this.socket.onerror = (error) => {
        console.error("❌ WebSocket Error:", error);
        this.connected = false;
      };

      this.socket.onclose = () => {
        console.warn("⚠️ WebSocket Disconnected! Reconnecting in 5s...");
        this.connected = false;
        setTimeout(this.connectWebSocket, 5000);
      };
    },
    getSensorName(sensorId) {
      return this.sensorNames[sensorId] || `Sensor ${sensorId}`;
    }
  }
};
</script>

<style scoped>
.container {
  text-align: center;
  font-family: Arial, sans-serif;
}
.sensor-card {
  background: #f4f4f4;
  padding: 15px;
  margin: 10px;
  border-radius: 5px;
  display: inline-block;
  width: 200px;
}
.error {
  color: red;
}
</style>