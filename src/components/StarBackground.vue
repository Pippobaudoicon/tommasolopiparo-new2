<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";

const props = defineProps<{ warp: boolean }>();
const canvasRef = ref<HTMLCanvasElement | null>(null);
let cleanup = () => {};

onMounted(() => {
  const canvas = canvasRef.value;
  const ctx = canvas?.getContext("2d");
  if (!canvas || !ctx) return;

  const motion = window.matchMedia("(prefers-reduced-motion: reduce)");
  const stars = Array.from({ length: 130 }, () => ({
    x: Math.random(), y: Math.random(),
    radius: Math.random() * 0.9 + 0.25,
    opacity: Math.random() * 0.5 + 0.12,
    depth: Math.random() * 0.7 + 0.3,
    phase: Math.random() * Math.PI * 2,
  }));

  let width = window.innerWidth;
  let height = window.innerHeight;
  let pointerX = 0;
  let pointerY = 0;
  let offsetX = 0;
  let offsetY = 0;
  let elapsed = 0;
  let warpLevel = 0;
  let previousTime = 0;
  let frame = 0;

  function draw(delta = 0) {
    if (!canvas || !ctx) return;
    elapsed += delta;
    const follow = 1 - Math.exp(-delta * 2);
    offsetX += (pointerX - offsetX) * follow;
    offsetY += (pointerY - offsetY) * follow;
    warpLevel += ((props.warp ? 1 : 0) - warpLevel) * (1 - Math.exp(-delta * 7));
    ctx.clearRect(0, 0, width, height);

    for (const star of stars) {
      const x = ((star.x * width + elapsed * star.depth * 1.1 + offsetX * star.depth) % width + width) % width;
      const y = ((star.y * height + elapsed * star.depth * 0.45 + offsetY * star.depth) % height + height) % height;
      const twinkle = motion.matches ? 1 : 0.8 + Math.sin(elapsed * 0.45 + star.phase) * 0.2;
      const alpha = star.opacity * twinkle;
      if (warpLevel > 0.01) {
        // Jumping between systems: stretch each star away from the centre.
        const stretch = warpLevel * star.depth * 0.35;
        ctx.strokeStyle = `rgba(200, 216, 244, ${Math.min(1, alpha * (1 + warpLevel))})`;
        ctx.lineWidth = star.radius * 1.6;
        ctx.beginPath();
        ctx.moveTo(x, y);
        ctx.lineTo(x + (x - width / 2) * stretch, y + (y - height / 2) * stretch);
        ctx.stroke();
        continue;
      }
      ctx.fillStyle = `rgba(200, 216, 244, ${alpha})`;
      ctx.beginPath();
      ctx.arc(x, y, star.radius, 0, Math.PI * 2);
      ctx.fill();
    }
  }

  function resize() {
    if (!canvas || !ctx) return;
    width = window.innerWidth;
    height = window.innerHeight;
    const ratio = Math.min(window.devicePixelRatio || 1, 2);
    canvas.width = Math.round(width * ratio);
    canvas.height = Math.round(height * ratio);
    ctx.setTransform(ratio, 0, 0, ratio, 0, 0);
    draw();
  }

  function animate(time: number) {
    const delta = previousTime ? Math.min((time - previousTime) / 1000, 0.05) : 0;
    previousTime = time;
    draw(delta);
    frame = requestAnimationFrame(animate);
  }

  function syncAnimation() {
    cancelAnimationFrame(frame);
    previousTime = 0;
    if (motion.matches) {
      pointerX = pointerY = offsetX = offsetY = 0;
    }
    draw();
    if (!motion.matches && !document.hidden) frame = requestAnimationFrame(animate);
  }

  function move(event: PointerEvent) {
    if (motion.matches || event.pointerType !== "mouse") return;
    pointerX = (event.clientX / width - 0.5) * 18;
    pointerY = (event.clientY / height - 0.5) * 18;
  }

  resize();
  syncAnimation();
  window.addEventListener("resize", resize);
  window.addEventListener("pointermove", move, { passive: true });
  motion.addEventListener("change", syncAnimation);
  document.addEventListener("visibilitychange", syncAnimation);

  cleanup = () => {
    cancelAnimationFrame(frame);
    window.removeEventListener("resize", resize);
    window.removeEventListener("pointermove", move);
    motion.removeEventListener("change", syncAnimation);
    document.removeEventListener("visibilitychange", syncAnimation);
  };
});

onUnmounted(() => cleanup());
</script>

<template>
  <canvas ref="canvasRef" class="star-canvas" aria-hidden="true" />
</template>

<style scoped>
.star-canvas {
  position: fixed;
  inset: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
}
</style>
