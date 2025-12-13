<template>
  <div :class="store.backgroundShow ? 'cover show' : 'cover'">
    <!-- 渐变背景 Canvas -->
    <canvas id="canvas-basic" class="gradient-bg"></canvas>
  </div>
</template>

<script setup>
import { onMounted, onBeforeUnmount } from "vue";
import Granim from "granim";
import { mainStore } from "@/store";

const store = mainStore();

let granimInstance = null;

onMounted(() => {
  granimInstance = new Granim({
    element: "#canvas-basic",
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
        transitionSpeed: 8000,
      },
    },
  });
});

onBeforeUnmount(() => {
  if (granimInstance) {
    granimInstance.destroy();
    granimInstance = null;
  }
});
</script>

<style lang="scss" scoped>
.cover {
  position: fixed;
  inset: 0;
  z-index: -1;
  opacity: 0;
  transition: opacity 0.6s ease;

  &.show {
    opacity: 1;
  }
}

.gradient-bg {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
}
</style>
