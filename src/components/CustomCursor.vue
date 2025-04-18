<template>
  <div
    class="custom-cursor"
    :style="{
      left: `${cursor.x}px`,
      top: `${cursor.y}px`
    }"
  ></div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";

const cursor = ref({ x: window.innerWidth / 2, y: window.innerHeight / 2 });
const target = ref({ x: window.innerWidth / 2, y: window.innerHeight / 2 });
const center = { x: window.innerWidth / 2, y: window.innerHeight / 2 };
let lastMove = Date.now();
let drifting = false;
let animationFrame: number | null = null;

// Set your desired speeds here
const FOLLOW_SPEED = 0.15; // speed when following mouse
const DRIFT_SPEED = 0.013;  // speed when drifting to center

function onMouseMove(e: MouseEvent) {
  target.value.x = e.clientX;
  target.value.y = e.clientY;
  lastMove = Date.now();
  drifting = false;
}

function animateCursor() {
  const dest = drifting ? center : target.value;
  const speed = drifting ? DRIFT_SPEED : FOLLOW_SPEED;
  cursor.value.x += (dest.x - cursor.value.x) * speed;
  cursor.value.y += (dest.y - cursor.value.y) * speed;
  animationFrame = requestAnimationFrame(animateCursor);
}

function checkIdle() {
  if (Date.now() - lastMove > 800 && !drifting) {
    drifting = true;
  } else if (drifting && Date.now() - lastMove <= 800) {
    drifting = false;
  }
  requestAnimationFrame(checkIdle);
}

onMounted(() => {
  window.addEventListener("mousemove", onMouseMove);
  animationFrame = requestAnimationFrame(animateCursor);
  requestAnimationFrame(checkIdle);
});

onUnmounted(() => {
  window.removeEventListener("mousemove", onMouseMove);
  if (animationFrame) cancelAnimationFrame(animationFrame);
});
</script>

<style scoped>
.custom-cursor {
  position: fixed;
  width: 32px;
  height: 32px;
  border-radius: 50%;
  background: rgba(255,255,255,0.4);
  pointer-events: none;
  transform: translate(-50%, -50%);
  z-index: 10000;
  transition: background 0.2s;
}
</style>