<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";

const cursor = ref<HTMLDivElement | null>(null);
let cleanup = () => {};

onMounted(() => {
  const element = cursor.value;
  if (!element) return;

  let x = 0;
  let y = 0;
  let targetX = 0;
  let targetY = 0;
  let frame = 0;
  let initialized = false;

  function animate() {
    x += (targetX - x) * 0.2;
    y += (targetY - y) * 0.2;
    element!.style.transform = `translate3d(${x}px, ${y}px, 0) translate(-50%, -50%)`;
    if (Math.abs(targetX - x) + Math.abs(targetY - y) > 0.1) {
      frame = requestAnimationFrame(animate);
    } else {
      frame = 0;
    }
  }

  function move(event: PointerEvent) {
    if (event.pointerType !== "mouse") return;
    targetX = event.clientX;
    targetY = event.clientY;
    if (!initialized) {
      x = targetX;
      y = targetY;
      initialized = true;
    }
    element!.style.opacity = "1";
    if (!frame) frame = requestAnimationFrame(animate);
  }

  function hide() { element!.style.opacity = "0"; }

  window.addEventListener("pointermove", move, { passive: true });
  document.documentElement.addEventListener("pointerleave", hide);
  window.addEventListener("blur", hide);
  cleanup = () => {
    cancelAnimationFrame(frame);
    window.removeEventListener("pointermove", move);
    document.documentElement.removeEventListener("pointerleave", hide);
    window.removeEventListener("blur", hide);
  };
});

onUnmounted(() => cleanup());
</script>

<template>
  <div ref="cursor" class="custom-cursor" aria-hidden="true" />
</template>

<style scoped>
.custom-cursor {
  position: fixed;
  top: 0;
  left: 0;
  width: 22px;
  height: 22px;
  border: 1px solid #b1c2df35;
  border-radius: 50%;
  pointer-events: none;
  opacity: 0;
  z-index: 100;
  transition: opacity 200ms;
}

@media (prefers-reduced-motion: reduce), (pointer: coarse) {
  .custom-cursor { display: none; }
}
</style>
