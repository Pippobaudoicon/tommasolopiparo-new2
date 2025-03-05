<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";

const canvasRef = ref<HTMLCanvasElement | null>(null);
let stars: { x: number; y: number; size: number; speed: number; driftX: number; driftY: number }[] = [];
const numStars = 100;
let mouseX = 0, mouseY = 0;

const createStars = (width: number, height: number) => {
  stars = Array.from({ length: numStars }, () => ({
    x: Math.random() * width,
    y: Math.random() * height,
    size: Math.random() * 2,
    speed: Math.random() * 0.3 + 0.1,
    driftX: (Math.random() - 1) * 0.3, // Slow horizontal drift
    driftY: (Math.random() - 1) * 0.3  // Slow vertical drift
  }));
};

const drawStars = (ctx: CanvasRenderingContext2D, width: number, height: number) => {
  ctx.clearRect(0, 0, width, height);
  ctx.fillStyle = "white";

  for (const star of stars) {
    // Move stars slightly in random directions (self-drift effect)
    star.x += star.driftX;
    star.y += star.driftY;

    // Keep stars within bounds
    if (star.x < 0) star.x = width;
    if (star.x > width) star.x = 0;
    if (star.y < 0) star.y = height;
    if (star.y > height) star.y = 0;

    // Apply slight movement based on mouse position
    const dx = (mouseX - width / 2) * star.speed * 0.05;
    const dy = (mouseY - height / 2) * star.speed * 0.05;

    ctx.beginPath();
    ctx.arc(star.x + dx, star.y + dy, star.size, 0, Math.PI * 2);
    ctx.fill();
  }
};

const animate = () => {
  const canvas = canvasRef.value;
  if (!canvas) return;

  const ctx = canvas.getContext("2d");
  if (!ctx) return;

  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;
  
  drawStars(ctx, canvas.width, canvas.height);
  requestAnimationFrame(animate);
};

const handleMouseMove = (event: MouseEvent) => {
  mouseX = event.clientX;
  mouseY = event.clientY;
};

onMounted(() => {
  const canvas = canvasRef.value;
  if (!canvas) return;

  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;
  createStars(canvas.width, canvas.height);

  window.addEventListener("mousemove", handleMouseMove);
  animate();
});

onUnmounted(() => {
  window.removeEventListener("mousemove", handleMouseMove);
});
</script>

<template>
  <canvas ref="canvasRef" class="star-canvas"></canvas>
</template>

<style scoped>
.star-canvas {
  position: fixed;
  top: 0;
  left: 0;
}
</style>
