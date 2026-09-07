<script setup lang="ts">
import { onMounted, onUnmounted, ref } from "vue";

const solarSystem = ref<HTMLElement | null>(null);
const hoveredPlanet = ref<string | null>(null);
const focusedPlanet = ref<string | null>(null);

const planets = [
  { name: "LinkedIn", href: "https://www.linkedin.com/in/tommasolopiparo", logo: "linkedin.svg", radius: 0.205, period: 24, angle: 3.9, size: 56, light: "#8ac4e6", color: "#236996", shadow: "#091829" },
  { name: "GitHub", href: "https://github.com/pippobaudoicon", logo: "github.svg", radius: 0.285, period: 34, angle: 0.7, size: 74, light: "#c4b3ed", color: "#69568d", shadow: "#1b142c" },
  { name: "Instagram", href: "https://www.instagram.com/tommilopi", logo: "instagram.svg", radius: 0.365, period: 46, angle: 2.8, size: 61, light: "#e6a7ba", color: "#974864", shadow: "#2a111f" },
  { name: "Contact me", href: "mailto:tommaso.lopiparo@gmail.com", logo: "email.svg", radius: 0.445, period: 60, angle: 5.6, size: 67, light: "#edb99c", color: "#ac6550", shadow: "#301a17" },
];

let cleanup = () => {};

onMounted(() => {
  const root = solarSystem.value;
  if (!root) return;

  const links = Array.from(root.querySelectorAll<HTMLAnchorElement>(".planet-container"));
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
    portraitRadius = root!.querySelector<HTMLElement>(".image-container")!.offsetWidth / 2;
  }

  measureBodies();

  function positionPlanets(delta = 0) {
    const positions = planets.map((planet, index) => {
      const paused = hoveredPlanet.value === planet.name || focusedPlanet.value === planet.name;
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
  <nav ref="solarSystem" class="solar-system" aria-label="Explore my solar system">
    <svg class="orbit-paths" viewBox="0 0 1000 1000" preserveAspectRatio="none" fill="none" aria-hidden="true">
      <circle v-for="planet in planets" :key="planet.name" cx="500" cy="500" :r="planet.radius * 1000" />
    </svg>

    <a
      v-for="planet in planets"
      :key="planet.name"
      :href="planet.href"
      :target="planet.href.startsWith('https:') ? '_blank' : undefined"
      :rel="planet.href.startsWith('https:') ? 'noopener noreferrer' : undefined"
      :aria-label="planet.name"
      class="planet-container"
      :class="{ 'is-active': hoveredPlanet === planet.name || focusedPlanet === planet.name }"
      :style="{
        '--size': planet.size + 'px',
        '--light': planet.light,
        '--color': planet.color,
        '--shadow': planet.shadow,
        transform: `translate(${Math.cos(planet.angle) * 100 * planet.radius}%, ${Math.sin(planet.angle) * 100 * planet.radius}%)`,
      }"
      @pointerenter="hoveredPlanet = $event.pointerType === 'mouse' ? planet.name : null"
      @pointerleave="hoveredPlanet = null"
      @focus="focusedPlanet = planet.name"
      @blur="focusedPlanet = null"
    >
      <span class="planet-ring" aria-hidden="true" />
      <span class="planet">
        <span class="planet-surface" aria-hidden="true" />
        <img :src="'/logos/' + planet.logo" alt="" class="planet-icon" width="28" height="28" />
      </span>
      <span class="planet-label" aria-hidden="true">{{ planet.name }} <span>↗</span></span>
    </a>

    <a class="image-container" href="/Lo%20Piparo%20CV.pdf" target="_blank" rel="noopener noreferrer" aria-label="View Tommaso Lo Piparo's résumé">
      <span class="solar-corona" aria-hidden="true" />
      <img src="/Tom.webp" alt="Tommaso Lo Piparo" class="face" fetchpriority="high" width="200" height="200" decoding="async" />
      <span class="resume-label" aria-hidden="true">View résumé ↗</span>
    </a>
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

.planet-label,
.resume-label {
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

.planet-container.is-active .planet-label,
.image-container:is(:hover, :focus-visible) .resume-label {
  opacity: 1;
  transform: translate(-50%, 0);
}

.image-container {
  position: absolute;
  left: 50%;
  top: 50%;
  width: min(172px, 18%, calc((100svh - 228px) * 0.28));
  aspect-ratio: 1;
  transform: translate(-50%, -50%);
  border-radius: 50%;
  z-index: 10;
}

.solar-corona {
  position: absolute;
  inset: -11%;
  border: 1px solid #a5c1e217;
  border-radius: 50%;
  background: radial-gradient(circle, #adc6ec00 50%, #87b2e415 70%, transparent 72%);
  box-shadow: 0 0 50px 15px #6e95c510;
  pointer-events: none;
}

.face {
  position: relative;
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: 50% 37%;
  border-radius: 50%;
  border: 1px solid #ced9ed35;
  filter: grayscale(0.85);
  box-shadow: 0 0 30px #0008;
  transition: filter 400ms, border-color 400ms;
}

.image-container:is(:hover, :focus-visible) .face {
  filter: grayscale(0.15);
  border-color: #c6d8f273;
}

@keyframes surface-drift {
  from { transform: translateY(-5%) rotate(-8deg); }
  to { transform: translateY(5%) rotate(-8deg); }
}

@media (max-width: 600px) {
  .solar-system {
    width: 92vw;
    height: auto;
    aspect-ratio: 1;
  }

  .image-container { width: 20%; }

  .planet-container {
    --mobile-size: calc(var(--size) * 0.62);
    width: max(44px, var(--mobile-size));
    height: max(44px, var(--mobile-size));
  }

  .planet { width: var(--mobile-size); height: var(--mobile-size); }

  .planet-ring {
    width: calc(var(--mobile-size) * 1.5);
    height: calc(var(--mobile-size) * 0.4);
  }

  .planet-label { font-size: 9px; top: calc(100% + 5px); }
  .resume-label { font-size: 9px; }
}

@media (hover: none) {
  .planet-label { opacity: 0.8; transform: translate(-50%, 0); }
}

@media (max-width: 600px) and (max-height: 650px) {
  .solar-system { width: min(92vw, calc(100svh - 260px)); }
}

@media (max-height: 550px) and (min-width: 601px) {
  .solar-system {
    width: 92vw;
    height: calc(100svh - 190px);
  }
  .planet-container { --size: 40px !important; }
}
</style>
