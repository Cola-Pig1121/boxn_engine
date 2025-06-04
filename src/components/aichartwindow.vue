<template>
  <div v-if="visible" class="aichart-overlay">
    <div class="aichart-window">
      <h2>AI Chart</h2>
      <div class="chart-container">
        <!-- 图表渲染区域 -->
        <canvas ref="chartCanvas"></canvas>
      </div>
      <button @click="close">Close</button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import Chart from 'chart.js/auto';

export default defineComponent({
  setup() {
    const visible = ref(false);
    const chartCanvas = ref<HTMLCanvasElement | null>(null);
    let chartInstance: Chart | null = null;

    const open = () => {
      visible.value = true;
    };

    const close = () => {
      visible.value = false;
      if (chartInstance) {
        chartInstance.destroy();
        chartInstance = null;
      }
    };

    onMounted(() => {
      if (chartCanvas.value) {
        const ctx = chartCanvas.value.getContext('2d');
        if (ctx) {
          chartInstance = new Chart(ctx, {
            type: 'line',
            data: {
              labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
              datasets: [{
                label: 'AI Performance',
                data: [65, 59, 80, 81, 56, 55],
                fill: false,
                borderColor: 'rgb(75, 192, 192)',
                tension: 0.1
              }]
            },
            options: {
              responsive: true,
              maintainAspectRatio: false
            }
          });
        }
      }
    });

    return {
      visible,
      chartCanvas,
      open,
      close
    };
  }
});
</script>

<style scoped>
.aichart-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0,0,0,0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1001; /* 比对话框高 */
}

.aichart-window {
  background: white;
  padding: 20px;
  border-radius: 8px;
  width: 600px;
  max-width: 90%;
}

.chart-container {
  height: 300px;
  margin: 20px 0;
}

button {
  padding: 8px 16px;
  background: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
</style>
