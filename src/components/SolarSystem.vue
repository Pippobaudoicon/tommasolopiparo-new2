<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";


export type Planet = {
  name: string;
  href?: string;
  logo?: string;
  radius: number;
  period: number;
  angle: number;
  size: number;
  light: string;
  color: string;
  shadow: string;
};

const props = defineProps<{ planets: Planet[]; label: string; selected?: string | null }>();
const emit = defineEmits<{ select: [name: string, element: HTMLElement] }>();

const solarSystem = ref<HTMLElement | null>(null);
const hoveredPlanet = ref<string | null>(null);
const focusedPlanet = ref<string | null>(null);

let cleanup = () => {};

onMounted(() => {
  const root = solarSystem.value;
  if (!root) return;

  const links = Array.from(root.querySelectorAll<HTMLElement>(".planet-container"));
  const planets = props.planets;
  const angles = planets.map(planet => planet.angle);
  const motion = window.matchMedia("(prefers-reduced-motion: reduce)");
  let width = root.clientWidth;
  let height = root.clientHeight;
  let frame = 0;
  let previousTime = 0;
  let bodyRadii: number[] = [];
  let portraitRadius = 0;

  function measureBodies() {
    // Reserve room for the 10% hover highlight as well as the planet itself.
    bodyRadii = links.map(link => link.querySelector<HTMLElement>(".planet")!.offsetWidth * 0.55);
    portraitRadius = root!.querySelector<HTMLElement>(".core")!.offsetWidth / 2;
  }

  measureBodies();

  function positionPlanets(delta = 0) {
    const positions = planets.map((planet, index) => {
      const paused = [hoveredPlanet.value, focusedPlanet.value, props.selected].includes(planet.name);
      if (!paused && !motion.matches) angles[index] += delta * Math.PI * 2 / planet.period;
      const angle = angles[index];
      const x = Math.cos(angle) * width * planet.radius;
      const y = Math.sin(angle) * height * planet.radius;
      const depth = Math.sin(angle);
      return { x, y, depth, paused, scale: 1 + depth * 0.08 };
    });

    // Let planets recede slightly during close passes instead of covering one
    // another. Their orbital paths and individual speeds remain unchanged.
    positions.forEach((position, index) => {
      const clearance = Math.hypot(position.x, position.y) - portraitRadius - 8;
      position.scale = Math.min(position.scale, Math.max(0, clearance) / bodyRadii[index]);

      for (let otherIndex = 0; otherIndex < index; otherIndex++) {
        const other = positions[otherIndex];
        const distance = Math.hypot(position.x - other.x, position.y - other.y);
        const occupied = bodyRadii[index] * position.scale + bodyRadii[otherIndex] * other.scale;
        const separation = Math.min(1, Math.max(0, distance - 8) / occupied);
        position.scale *= separation;
        other.scale *= separation;
      }
    });

    positions.forEach(({ x, y, depth, paused, scale }, index) => {
      const link = links[index];
      link.style.transform = `translate3d(${x}px, ${y}px, 0) translate(-50%, -50%) scale(${scale})`;
      link.style.zIndex = paused ? "30" : depth > 0 ? "20" : "5";
    });
  }

  function animate(time: number) {
    const delta = previousTime ? Math.min((time - previousTime) / 1000, 0.05) : 0;
    previousTime = time;
    positionPlanets(delta);
    frame = requestAnimationFrame(animate);
  }

  function syncAnimation() {
    cancelAnimationFrame(frame);
    previousTime = 0;
    positionPlanets();
    if (!document.hidden && !motion.matches) frame = requestAnimationFrame(animate);
  }

  const observer = new ResizeObserver(() => {
    width = root.clientWidth;
    height = root.clientHeight;
    measureBodies();
    positionPlanets();
  });
  observer.observe(root);
  motion.addEventListener("change", syncAnimation);
  document.addEventListener("visibilitychange", syncAnimation);
  syncAnimation();

  cleanup = () => {
    cancelAnimationFrame(frame);
    observer.disconnect();
    motion.removeEventListener("change", syncAnimation);
    document.removeEventListener("visibilitychange", syncAnimation);
  };
});

onUnmounted(() => cleanup());
</script>

<template>
  <nav ref="solarSystem" class="solar-system" :aria-label="label">
    <svg class="orbit-paths" viewBox="0 0 1000 1000" preserveAspectRatio="none" fill="none" aria-hidden="true">
      <circle v-for="radius in new Set(planets.map(planet => planet.radius))" :key="radius" cx="500" cy="500" :r="radius * 1000" />
    </svg>

    <div
      v-for="planet in planets"
      :key="planet.name"
      class="planet-container"
      :class="{ 'is-active': hoveredPlanet === planet.name || focusedPlanet === planet.name || selected === planet.name }"
      :style="{
        '--size': planet.size + 'px',
        '--light': planet.light,
        '--color': planet.color,
        '--shadow': planet.shadow,
      }"
      @pointerenter="hoveredPlanet = $event.pointerType === 'mouse' ? planet.name : null"
      @pointerleave="hoveredPlanet = null"
      @focusin="focusedPlanet = planet.name"
      @focusout="focusedPlanet = null"
    >
      <component
        :is="planet.href ? 'a' : 'button'"
        :href="planet.href"
        :type="planet.href ? undefined : 'button'"
        :target="planet.href?.startsWith('https:') ? '_blank' : undefined"
        :rel="planet.href?.startsWith('https:') ? 'noopener noreferrer' : undefined"
        :aria-label="planet.name"
        :aria-pressed="planet.href || selected === undefined ? undefined : selected === planet.name"
        class="planet-link"
        @click="!planet.href && emit('select', planet.name, $event.currentTarget)"
      >
        <span class="planet-ring" aria-hidden="true" />
        <span class="planet">
          <span class="planet-surface" aria-hidden="true" />
          <slot name="surface" :planet="planet" />
          <img v-if="planet.logo" :src="'/logos/' + planet.logo" alt="" class="planet-icon" width="28" height="28" />
        </span>
        <span class="planet-label" aria-hidden="true">{{ planet.name }} <span v-if="planet.href">↗</span></span>
      </component>
      <!-- Outside the link, so a companion can hold its own buttons. -->
      <slot name="companion" :planet="planet" />
    </div>

    <div class="core">
      <slot />
    </div>
  </nav>
</template>

<style scoped>
.solar-system {
  position: relative;
  width: min(1600px, 92vw);
  height: min(720px, calc(100svh - 228px), calc(92vw / 1.55));
  flex-shrink: 0;
  isolation: isolate;
}

.orbit-paths {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  overflow: visible;
  pointer-events: none;
}

.orbit-paths circle {
  stroke: #8299bd;
  stroke-opacity: 0.13;
  stroke-width: 1;
  vector-effect: non-scaling-stroke;
}

.orbit-paths circle:nth-child(even) {
  stroke-opacity: 0.085;
}

.planet-container {
  position: absolute;
  top: 50%;
  left: 50%;
  width: max(44px, var(--size));
  height: max(44px, var(--size));
  display: grid;
  place-items: center;
  border-radius: 50%;
  will-change: transform;
}

.planet-link {
  position: absolute;
  inset: 0;
  display: grid;
  place-items: center;
  padding: 0;
  border: 0;
  border-radius: 50%;
  background: none;
  color: inherit;
  cursor: pointer;
}

.planet {
  position: relative;
  display: grid;
  place-items: center;
  width: var(--size);
  height: var(--size);
  border-radius: 50%;
  overflow: hidden;
  background: radial-gradient(circle at 28% 23%, var(--light), var(--color) 37%, var(--shadow) 78%);
  box-shadow: inset -6px -7px 12px #0008, inset 1px 1px 2px #ffffff40, 0 0 24px color-mix(in srgb, var(--color) 18%, transparent);
  transition: scale 250ms, box-shadow 250ms;
  z-index: 1;
}

.planet-surface {
  position: absolute;
  inset: -30%;
  opacity: 0.15;
  background: repeating-linear-gradient(168deg, transparent 0 8px, #ffffff38 10px 11px, transparent 14px 21px);
  animation: surface-drift 35s linear infinite;
}

.planet-icon {
  width: 42%;
  height: 42%;
  object-fit: contain;
  filter: brightness(0) invert(1);
  opacity: 0.82;
  z-index: 2;
  transition: opacity 250ms;
}

.planet-ring {
  position: absolute;
  width: calc(var(--size) * 1.6);
  height: calc(var(--size) * 0.45);
  border: 1px solid color-mix(in srgb, var(--light) 35%, transparent);
  border-radius: 50%;
  transform: rotate(-28deg);
  box-shadow: 0 0 0 3px color-mix(in srgb, var(--color) 8%, transparent);
  pointer-events: none;
}

.planet-container:nth-of-type(even) .planet-ring { transform: rotate(24deg); }

.planet-container.is-active .planet {
  scale: 1.1;
  box-shadow: inset -6px -7px 12px #0008, inset 1px 1px 2px #ffffff55, 0 0 36px color-mix(in srgb, var(--color) 40%, transparent);
}

.planet-container.is-active .planet-icon { opacity: 1; }
.planet-container.is-active .planet-surface { animation-play-state: paused; }

.planet-label {
  position: absolute;
  top: calc(100% + 13px);
  left: 50%;
  transform: translate(-50%, -3px);
  white-space: nowrap;
  font-size: 11px;
  letter-spacing: 0.04em;
  color: #d5ddec;
  opacity: 0;
  transition: opacity 200ms, transform 200ms;
  pointer-events: none;
}

.planet-label span { margin-left: 4px; color: #8c9bb4; }

.planet-container.is-active .planet-label {
  opacity: 1;
  transform: translate(-50%, 0);
}

.core {
  position: absolute;
  left: 50%;
  top: 50%;
  width: min(172px, 18%, calc((100svh - 228px) * 0.28));
  aspect-ratio: 1;
  transform: translate(-50%, -50%);
  z-index: 10;
}

@keyframes surface-drift {
  from { transform: translateY(-5%) rotate(-8deg); }
  to { transform: translateY(5%) rotate(-8deg); }
}

@media (max-width: 600px) {
  .solar-system {
    width: 112vw;
    height: min(140vw, calc(100svh - 284px));
  }

  .core {
    width: clamp(88px, 26vw, 120px);
    height: clamp(88px, 26vw, 120px);
  }

  .planet-container {
    --mobile-size: calc(var(--size) * 0.82);
    width: max(44px, var(--mobile-size));
    height: max(44px, var(--mobile-size));
  }

  .planet { width: var(--mobile-size); height: var(--mobile-size); }

  .planet-ring {
    width: calc(var(--mobile-size) * 1.5);
    height: calc(var(--mobile-size) * 0.4);
  }

  .planet-label { font-size: 10px; top: calc(100% + 5px); }
}

@media (hover: none) {
  .planet-label { opacity: 0.8; transform: translate(-50%, 0); }
}

@media (max-width: 600px) and (max-height: 650px) {
  .solar-system { height: calc(100svh - 260px); }
}

@media (max-width: 600px) and (prefers-reduced-motion: reduce) {
  /* Stationary planets should stay reachable without waiting for an orbit. */
  .solar-system { width: 92vw; }
}

@media (max-height: 550px) and (min-width: 601px) {
  .solar-system {
    width: 92vw;
    height: calc(100svh - 190px);
  }
  .planet-container { --size: 40px !important; }
}
</style>
