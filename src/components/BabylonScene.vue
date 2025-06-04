<script setup lang="ts">
import { onMounted, onBeforeUnmount, ref } from 'vue';
import { BoxNextEngine } from '../scripts/babylon/framework/BoxNextEngine';
import cannon from "cannon";
import DialogBox from './dialogbox.vue';
import AichartWindow from './aichartwindow.vue';

const dialogBox = ref<InstanceType<typeof DialogBox> | null>(null);
const aichartWindow = ref<InstanceType<typeof AichartWindow> | null>(null);
const canvasRef = ref<HTMLCanvasElement | null>(null);

const handleMessageSent = (message: string) => {
  if (message.trim().toLowerCase() === '/aichat') {
    aichartWindow.value?.open();
  }
};

function setupInputListeners() {
  const scene = BoxNextEngine.instance?.scene;
  if (!scene) return;
  
  scene.onKeyboardObservable.add((kbInfo) => {
    if (kbInfo.event.key === 't' && kbInfo.type === 1) { // 1 = KEYDOWN
      dialogBox.value?.open();
    }
  });
}

window.CANNON = cannon;

onMounted(() => {
  if (canvasRef.value) {
    BoxNextEngine.instance.initialize(canvasRef.value);
    setupInputListeners();
  }
});

onBeforeUnmount(() => {
  BoxNextEngine.instance.dispose();
});
</script>

<template>
  <div>
    <canvas ref="canvasRef" />
    <DialogBox ref="dialogBox" @message-sent="handleMessageSent" />
    <AichartWindow ref="aichartWindow" />
  </div>
</template>


<style scoped>
.scene-container {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.scene-canvas {
  width: 100%;
  height: 100%;
  outline: none;
  touch-action: none;
}
</style> 