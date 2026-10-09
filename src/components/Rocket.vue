<script setup lang="ts">
const emit = defineEmits<{ launch: [element: HTMLElement] }>();
</script>

<template>
  <button type="button" class="rocket" aria-label="Fly to my projects" @click="emit('launch', $event.currentTarget as HTMLElement)">
    <span class="exhaust" aria-hidden="true"><span /><span /><span /></span>
    <svg class="ship" viewBox="0 0 24 32" aria-hidden="true">
      <path class="flame" d="M9.6 23.5 12 31l2.4-7.5Z" />
      <path class="fin" d="M7.6 15.5 3.5 20.5v4l4.1-3M16.4 15.5l4.1 5v4l-4.1-3" />
      <path class="body" d="M12 1c3.7 2.7 5 6.9 5 11.4V23.5H7V12.4C7 7.9 8.3 3.7 12 1Z" />
      <circle class="window" cx="12" cy="11" r="2.3" />
    </svg>
    <span class="label">Projects</span>
  </button>
</template>

<style scoped>
.rocket {
  position: absolute;
  top: 24px;
  left: 28px;
  width: 70px;
  height: 70px;
  display: grid;
  place-items: center;
  padding: 0;
  border: 0;
  border-radius: 50%;
  background: radial-gradient(circle, #4f8d7a26, transparent 65%);
  color: inherit;
  cursor: pointer;
  z-index: 100;
}

/* Pointing up and to the right, as if it were already leaving. */
.ship {
  width: 30px;
  height: 40px;
  rotate: 45deg;
  overflow: visible;
  animation: drift 3.2s ease-in-out infinite;
  transition: translate 300ms;
}

.body { fill: #d3efe3; }
.window { fill: #10261f; stroke: #4f8d7a; stroke-width: 1.2; }
.fin { fill: #4f8d7a; stroke: #4f8d7a; stroke-width: 1; stroke-linejoin: round; }

.flame {
  fill: #f0b37a;
  transform-origin: 12px 23.5px;
  animation: flicker 180ms ease-in-out infinite alternate;
  filter: drop-shadow(0 0 3px #f0b37a);
}

/* Little puffs left behind, down and to the left of the ship. */
.exhaust { position: absolute; inset: 0; pointer-events: none; }

.exhaust span {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 4px;
  height: 4px;
  border-radius: 50%;
  background: #d3efe3;
  opacity: 0;
  animation: puff 1.8s ease-out infinite;
}

.exhaust span:nth-child(2) { animation-delay: 0.6s; }
.exhaust span:nth-child(3) { animation-delay: 1.2s; }

.label {
  position: absolute;
  top: calc(100% - 4px);
  left: 50%;
  transform: translate(-50%, -3px);
  white-space: nowrap;
  font-size: 10px;
  color: #a1aabd;
  opacity: 0;
  transition: opacity 300ms, transform 300ms;
}

.rocket:is(:hover, :focus-visible) .ship { translate: 3px -3px; }
.rocket:is(:hover, :focus-visible) .flame { animation-duration: 80ms; scale: 1 1.6; }
.rocket:is(:hover, :focus-visible) .exhaust span { animation-duration: 0.9s; }
.rocket:is(:hover, :focus-visible) .label { opacity: 1; transform: translate(-50%, 0); }

@keyframes drift { 50% { transform: translate(1.5px, -1.5px); } }
@keyframes flicker { to { transform: scaleY(0.7); opacity: 0.8; } }
@keyframes puff {
  from { transform: translate(-8px, 6px) scale(1); opacity: 0.6; }
  to { transform: translate(-26px, 24px) scale(0.3); opacity: 0; }
}

@media (hover: none) {
  .label { opacity: 0.8; transform: translate(-50%, 0); }
}

@media (max-width: 600px) {
  .rocket { top: 10px; left: 10px; width: 56px; height: 56px; }
  .ship { width: 24px; height: 32px; }
}
</style>
