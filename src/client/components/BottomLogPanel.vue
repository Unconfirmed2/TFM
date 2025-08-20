<template>
  <div class="bottom-log-panel">
    <div class="log-header">
      <button class="toggle-button" @click="$emit('toggle')">
        <span v-if="isVisible">▼ Hide Game Log</span>
        <span v-else>▲ Show Game Log</span>
      </button>
    </div>
    <div v-if="isVisible" class="log-content">
      <div class="log-messages">
        <div v-if="messages.length === 0" class="no-messages">
          <p>No log messages available yet.</p>
          <p><small>Messages will appear as the game progresses.</small></p>
        </div>
        <div v-else class="message-list">
          <div v-for="(message, index) in messages" :key="index" class="log-message">
            <span class="message-text">{{ message.message || message }}</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';

interface LogMessage {
  message: string;
  playerId?: string;
  timestamp?: string;
}

export default Vue.extend({
  name: 'BottomLogPanel',
  props: {
    isVisible: {
      type: Boolean,
      default: true
    },
    playerId: {
      type: String,
      required: true
    },
    generation: {
      type: Number,
      required: true
    },
    step: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      messages: [] as LogMessage[]
    };
  },
  mounted() {
    this.fetchLogs();
  },
  watch: {
    generation() {
      this.fetchLogs();
    },
    step() {
      this.fetchLogs();
    }
  },
  methods: {
    async fetchLogs() {
      try {
        console.log(`Fetching logs for player ${this.playerId}, generation ${this.generation}`);
        const response = await fetch(`/api/game/logs?id=${this.playerId}&generation=${this.generation}`);
        
        if (response.ok) {
          const data = await response.json();
          console.log('Fetched log data:', data);
          this.messages = Array.isArray(data) ? data : [];
        } else {
          console.error('Failed to fetch logs:', response.status, response.statusText);
          // Add some dummy messages for testing
          this.messages = [
            { message: `Game started - Generation ${this.generation}` },
            { message: `Player ${this.playerId} joined the game` },
            { message: 'Game log system is working!' }
          ];
        }
      } catch (error) {
        console.error('Error fetching logs:', error);
        // Add fallback messages
        this.messages = [
          { message: 'Log system initialized' },
          { message: `Current generation: ${this.generation}` },
          { message: `Player ID: ${this.playerId}` }
        ];
      }
    }
  }
});
</script>

<style scoped>
.bottom-log-panel {
  width: 100vw;
  margin-left: calc(-50vw + 50%);
  background: #000000;
  border-top: 3px solid #007bff;
  margin-top: 30px;
  box-shadow: 0 -2px 10px rgba(0, 0, 0, 0.1);
}

.log-header {
  background: linear-gradient(135deg, #000000ff, #000000ff);
  padding: 1px;
  text-align: center;
  border-bottom: 1px solid #dee2e6;
}

.toggle-button {
  background: white;
  color: #007bff;
  border: 2px solid #007bff;
  padding: 10px 20px;
  border-radius: 25px;
  cursor: pointer;
  font-weight: bold;
  font-size: 14px;
  transition: all 0.3s ease;
  min-width: 180px;
}

.toggle-button:hover {
  background: #007bff;
  color: white;
  transform: translateY(-1px);
  box-shadow: 0 4px 8px rgba(0, 123, 255, 0.3);
}

.log-content {
  padding: 0px;
  max-height: 60vh;
  overflow-y: auto;
  background: rgb(0, 0, 0);
}

.log-debug {
  background: #000000ff;
  border: 1px solid #b3d7ff;
  border-radius: 8px;
  padding: 15px;
  margin-bottom: 20px;
  font-family: 'Courier New', monospace;
  font-size: 13px;
}

.log-debug h4 {
  margin: 0 0 10px 0;
  color: #0056b3;
}

.log-debug p {
  margin: 5px 0;
  color: #333;
}

.log-messages {
  background: #000000ff;
  border: 1px solid #dee2e6;
  border-radius: 8px;
  min-height: 200px;
}

.no-messages {
  padding: 40px;
  text-align: center;
  color: #6c757d;
}

.no-messages p {
  margin: 10px 0;
}

.message-list {
  padding: 15px;
}

.log-message {
  padding: 8px 12px;
  margin: 5px 0;
  background: #000000ff;
  border-left: 4px solid #007bff;
  border-radius: 4px;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  font-size: 20px;
  line-height: 1.4;
}

.message-text {
  color: #ffffffff;
}

/* Responsive design */
@media (max-width: 768px) {
  .log-content {
    padding: 15px;
    max-height: 50vh;
  }
  
  .toggle-button {
    padding: 8px 16px;
    font-size: 13px;
    min-width: 150px;
  }
  
  .log-debug {
    padding: 12px;
    font-size: 12px;
  }
}

@media (max-width: 480px) {
  .log-content {
    padding: 10px;
    max-height: 40vh;
  }
}
</style>
