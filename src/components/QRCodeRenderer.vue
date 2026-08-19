<template>
  <div class="relative flex flex-col items-center justify-center select-none font-mono">
    
    <!-- Cyber HUD Outer Ring -->
    <div class="relative p-5 rounded-2xl bg-neutral-950/90 border border-red-600/70 shadow-[0_0_35px_rgba(255,0,50,0.5)] group backdrop-blur-xl">
      
      <!-- Top Corner Accents -->
      <span class="absolute -top-1 -left-1 w-4 h-4 border-t-2 border-l-2 border-red-500" />
      <span class="absolute -top-1 -right-1 w-4 h-4 border-t-2 border-r-2 border-red-500" />
      <span class="absolute -bottom-1 -left-1 w-4 h-4 border-b-2 border-l-2 border-red-500" />
      <span class="absolute -bottom-1 -right-1 w-4 h-4 border-b-2 border-r-2 border-red-500" />

      <!-- Scanning HUD Ring Overlay -->
      <div v-if="isScanning" class="absolute -inset-3 rounded-full border-2 border-red-500/60 border-dashed animate-[spin_6s_linear_infinite] pointer-events-none" />

      <!-- QR Canvas Image -->
      <div class="relative bg-white p-3 rounded-lg overflow-hidden shadow-inner">
        <canvas ref="canvasRef" class="w-44 h-44 sm:w-52 sm:h-52 block" />
        
        <!-- Subtle Scanline texture over QR code -->
        <div class="absolute inset-0 bg-gradient-to-b from-transparent via-red-500/10 to-transparent pointer-events-none animate-pulse" />
      </div>

    </div>

    <!-- Instruction Label -->
    <div class="mt-4 flex items-center gap-2 text-xs text-red-400 tracking-[0.25em] uppercase font-bold">
      <span class="inline-block w-2 h-2 bg-red-600 rounded-full animate-ping" />
      <span>{{ label || 'SCAN TO CONTINUE' }}</span>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, watch } from 'vue';
import QRCode from 'qrcode';

const props = defineProps<{
  value: string;
  label?: string;
  isScanning?: boolean;
}>();

const canvasRef = ref<HTMLCanvasElement | null>(null);

const renderQR = async () => {
  if (!canvasRef.value) return;
  try {
    await QRCode.toCanvas(canvasRef.value, props.value, {
      width: 250,
      margin: 1,
      color: {
        dark: '#111827',
        light: '#ffffff'
      }
    });
  } catch (err) {
    console.error('Failed to generate QR code:', err);
  }
};

onMounted(() => {
  renderQR();
});

watch(() => props.value, () => {
  renderQR();
});
</script>
