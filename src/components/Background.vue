<template>
  <canvas ref="canvasRef" class="gradient-bg"></canvas>
</template>

<script setup>
import { onMounted, onBeforeUnmount, ref, nextTick } from "vue";
import Granim from "granim";

const canvasRef = ref(null);
let granim = null;

onMounted(async () => {
  await nextTick();

  granim = new Granim({
    element: canvasRef.value,
    direction: "left-right",
    isPausedWhenNotInView: true,
    states: {
      "default-state": {
        gradients: [
          ["#a18cd1", "#fbc2eb"],
          ["#fff1eb", "#ace0f9"],
          ["#d4fc79", "#96e6a1"],
          ["#a1c4fd", "#c2e9fb"],
          ["#a8edea", "#fed6e3"],
          ["#9890e3", "#b1f4cf"],
        ],
        transitionSpeed: 6000,
      },
    },
  });
});

onBeforeUnmount(() => {
  granim?.destroy?.();
});
</script>

<style scoped>
.gradient-bg {
  position: fixed;
  inset: 0;
  width: 100%;
  height: 100%;
  z-index: -999;
}
</style>
