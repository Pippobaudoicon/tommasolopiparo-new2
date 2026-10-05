<script setup lang="ts">
import { computed, nextTick, onMounted, onUnmounted, ref } from "vue";
import SolarSystem, { type Planet } from "./SolarSystem.vue";

const projects = [
  {
    name: "ChatLDS",
    id: "chatlds",
    logo: "chat.svg",
    tagline: "A study companion that cites its sources.",
    description: "Ask about scriptures or conference talks: it retrieves the passages, streams an answer and links every citation. Saved conversations keep the study going.",
    stack: ["Next.js", "TypeScript", "RAG", "Postgres", "Redis"],
    site: "https://chatlds.it",
    repo: "https://github.com/Pippobaudoicon/rag-chat",
    planet: { radius: 0.205, period: 26, angle: 0.4, size: 64, light: "#a9c9f0", color: "#3d6aa3", shadow: "#0d1a2e" },
  },
  {
    name: "Deathroll",
    id: "deathroll",
    logo: "dice.svg",
    tagline: "World of Warcraft's dice duel, now outside Azeroth.",
    description: "Real-time rooms for up to 8 players: roll, lower the ceiling, pray you don't hit 1. A small tribute to the hours spent rolling for loot with strangers.",
    stack: ["Vue", "Node.js", "Express", "Socket.IO"],
    site: "https://deathroll.tommasolopiparo.com",
    repo: "https://github.com/Pippobaudoicon/deathroll-wow",
    planet: { radius: 0.285, period: 36, angle: 2.6, size: 58, light: "#f0c08a", color: "#a8642c", shadow: "#2e1a0c" },
  },
  {
    name: "PokéRole Roller",
    id: "pokerole",
    tagline: "Every dice roll for a PokéRole v3 table session.",
    description: "Success pools, damage, chance dice and criticals worked out for you, plus a searchable catalogue of 305 abilities and a 1,200-species Pokédex to build your team.",
    stack: ["React", "TypeScript", "Vite"],
    site: "https://pokerole.tommasolopiparo.com",
    repo: "https://github.com/Pippobaudoicon/pokerole",
    planet: { radius: 0.365, period: 48, angle: 4.4, size: 70, light: "#efa3a3", color: "#a23d3d", shadow: "#2c0e0e" },
  },
  {
    name: "Reaction Test",
    id: "reaction",
    logo: "bolt.svg",
    tagline: "How fast are you, really?",
    description: "A reaction game I first wrote in 2021, rebuilt with five modes across mouse, keyboard and touch, and a global leaderboard to keep you honest.",
    stack: ["JavaScript", "Cloudflare Workers", "D1"],
    site: "https://reaction.tommasolopiparo.com",
    repo: "https://github.com/Pippobaudoicon/little-game",
    planet: { radius: 0.445, period: 62, angle: 5.8, size: 54, light: "#c9b2f0", color: "#6e50a6", shadow: "#1c1230" },
  },
];

const planets: Planet[] = projects.map(project => ({ name: project.name, logo: project.logo, ...project.planet }));
// Each world gets its own surface and companion, styled below by project id.
const idOf = (name: string) => projects.find(project => project.name === name)!.id;

const selected = ref<string | null>(null);
// The log opens on the side opposite the chosen planet so it never covers it.
const side = ref<"left" | "right">("right");
const log = ref<HTMLElement | null>(null);
const index = computed(() => projects.findIndex(project => project.name === selected.value));
const project = computed(() => projects[index.value]);

async function select(name: string, element: HTMLElement) {
  if (selected.value === name) return close();
  const rect = element.getBoundingClientRect();
  side.value = rect.left + rect.width / 2 > window.innerWidth / 2 ? "left" : "right";
  selected.value = name;
  await nextTick();
  log.value?.focus();
}

function close() {
  selected.value = null;
}

function onKey(event: KeyboardEvent) {
  if (event.key === "Escape") close();
}

onMounted(() => window.addEventListener("keydown", onKey));
onUnmounted(() => window.removeEventListener("keydown", onKey));
</script>

<template>
  <SolarSystem :planets="planets" label="My projects" :selected="selected" @select="select">
    <slot />
    <template #surface="{ planet }">
      <span class="world" :class="idOf(planet.name)" aria-hidden="true" />
    </template>
    <template #companion="{ planet }">
      <span class="companion" :class="idOf(planet.name)" aria-hidden="true">
        <span v-if="idOf(planet.name) === 'chatlds'" class="orbiter satellite" />
        <template v-else-if="idOf(planet.name) === 'deathroll'">
          <span class="orbiter die" />
          <span class="orbiter die second" />
        </template>
        <span v-else-if="idOf(planet.name) === 'pokerole'" class="orbiter moon" />
        <template v-else>
          <span class="pulse" />
          <span class="pulse second" />
        </template>
      </span>
    </template>
  </SolarSystem>

  <Transition name="log">
    <section
      v-if="project"
      ref="log"
      :key="project.name"
      class="mission-log"
      :class="side"
      :style="{ '--light': project.planet.light, '--color': project.planet.color }"
      tabindex="-1"
      aria-labelledby="mission-title"
    >
      <header class="log-head">
        <span class="mission-id">Mission {{ String(index + 1).padStart(2, "0") }}</span>
        <span class="status"><span class="status-dot" aria-hidden="true" />Live</span>
        <button type="button" class="close" aria-label="Close mission log" @click="close">×</button>
      </header>
      <h2 id="mission-title">{{ project.name }}</h2>
      <p class="tagline">{{ project.tagline }}</p>
      <p class="description">{{ project.description }}</p>
      <p class="payload-label">Payload</p>
      <ul class="payload">
        <li v-for="tech in project.stack" :key="tech">{{ tech }}</li>
      </ul>
      <div class="actions">
        <a class="action primary" :href="project.site" target="_blank" rel="noopener noreferrer">Launch site <span>↗</span></a>
        <a class="action" :href="project.repo" target="_blank" rel="noopener noreferrer">
          <img src="/logos/github.svg" alt="" width="14" height="14" />Source code <span>↗</span>
        </a>
      </div>
    </section>
  </Transition>
</template>

<style scoped>
/* Worlds: a texture painted inside each planet, over its lit sphere. */
.world { position: absolute; inset: 0; border-radius: 50%; }

.world.chatlds {
  background: repeating-linear-gradient(172deg, transparent 0 6px, #b9d6f533 6px 9px, transparent 9px 15px, #0b182a40 15px 17px);
  background-size: 100% 200%;
  animation: bands 30s linear infinite;
}

.world.deathroll {
  background:
    radial-gradient(circle at 26% 34%, #3a1d0acc 0 9%, #f0c08a40 10% 11.5%, transparent 12.5%),
    radial-gradient(circle at 68% 64%, #3a1d0acc 0 7%, #f0c08a40 8% 9.5%, transparent 10.5%),
    radial-gradient(circle at 38% 76%, #3a1d0acc 0 4.5%, transparent 5.5%),
    radial-gradient(circle at 74% 26%, #3a1d0acc 0 3.5%, transparent 4.5%);
}

.world.pokerole {
  background:
    radial-gradient(circle at 50% 50%, #f4efe8 0 9%, #1a0f0f 10% 16%, transparent 17%),
    radial-gradient(circle at 28% 23%, #ffffff66, transparent 42%),
    radial-gradient(circle at 70% 78%, #0009, transparent 62%),
    linear-gradient(180deg, #c54444 0 46%, #1a0f0f 46% 54%, #e9e3dc 54%);
}

.world.reaction {
  background: radial-gradient(circle at 50% 50%, #f1e9ff 0 10%, #c9b2f0aa 18%, transparent 46%);
  animation: charge 2.4s ease-in-out infinite;
}

@keyframes bands { to { background-position: 0 100%; } }
@keyframes charge { 50% { opacity: 0.45; } }

/* Companions: something in orbit around each world. */
.companion {
  position: absolute;
  inset: calc(var(--size) * -0.32);
  pointer-events: none;
}

.orbiter {
  position: absolute;
  inset: 0;
  animation: circle 7s linear infinite;
}

.orbiter::before {
  content: "";
  position: absolute;
  top: 0;
  left: 50%;
}

.satellite::before {
  width: 4px;
  height: 4px;
  margin-left: -2px;
  background: #e3eefc;
  box-shadow: -6px 0 0 -0.5px #7fa8db, 6px 0 0 -0.5px #7fa8db, 0 0 8px #a9c9f0;
}

.die { animation-duration: 9s; }
.die.second { animation-duration: 13s; animation-direction: reverse; inset: 10%; }

.die::before {
  width: 8px;
  height: 8px;
  margin-left: -4px;
  border-radius: 2px;
  background: radial-gradient(circle, #2e1a0c 0 1.2px, transparent 1.6px), #f0c08a;
  animation: tumble 2.8s linear infinite;
}

.die.second::before { width: 6px; height: 6px; margin-left: -3px; }

.moon { animation-duration: 11s; }

.moon::before {
  width: 7px;
  height: 7px;
  margin-left: -3.5px;
  border-radius: 50%;
  background: radial-gradient(circle at 30% 30%, #fff, #efa3a3 60%, #6b2424);
}

.pulse {
  position: absolute;
  inset: calc(var(--size) * 0.32); /* the planet's edge */
  border: 1px solid #c9b2f0;
  border-radius: 50%;
  animation: pulse 2.4s ease-out infinite;
}

.pulse.second { animation-delay: 1.2s; }

@keyframes circle { to { transform: rotate(360deg); } }
@keyframes tumble { to { transform: rotate(360deg); } }
@keyframes pulse {
  from { transform: scale(0.95); opacity: 0.7; }
  to { transform: scale(1.5); opacity: 0; }
}

/* Companions replace the decorative ring, and each world reacts to hover in its own way. */
.solar-system :deep(.planet-ring) { display: none; }
:deep(.is-active) .world.chatlds { animation-duration: 6s; }
:deep(.is-active) .orbiter { animation-duration: 2.5s; }
:deep(.is-active) .die::before { animation-duration: 0.5s; }
:deep(.is-active) .world.reaction,
:deep(.is-active) .pulse { animation-duration: 0.8s; }
:deep(.is-active) .pulse.second { animation-delay: 0.4s; }

.mission-log {
  position: absolute;
  top: 50%;
  width: min(330px, 30vw);
  padding: 20px 22px 22px;
  translate: 0 -50%;
  background: #0a0e16e8;
  border: 1px solid #a9c1e21c;
  border-top: 2px solid color-mix(in srgb, var(--light) 55%, transparent);
  border-radius: 6px;
  box-shadow: 0 20px 60px #000a, 0 0 50px color-mix(in srgb, var(--color) 12%, transparent);
  backdrop-filter: blur(10px);
  z-index: 60;
  text-align: left;
}

.mission-log:focus { outline: none; }
.mission-log:focus-visible { outline: 2px solid #9fbbec; outline-offset: 4px; }
.mission-log.left { left: max(24px, 3vw); }
.mission-log.right { right: max(24px, 3vw); }

.log-head {
  display: flex;
  align-items: center;
  gap: 12px;
  font: 10px/1 ui-monospace, "SF Mono", Menlo, monospace;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: #8c9bb4;
}

.status { display: inline-flex; align-items: center; gap: 6px; color: #9fd4bd; }

.status-dot {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: #7fd1ad;
  box-shadow: 0 0 8px #7fd1ad;
  animation: blink 2.4s ease-in-out infinite;
}

@keyframes blink { 50% { opacity: 0.35; } }

.close {
  margin-left: auto;
  width: 28px;
  height: 28px;
  border: 0;
  border-radius: 50%;
  background: none;
  color: #a1aabd;
  font-size: 18px;
  line-height: 1;
  cursor: pointer;
}

.close:hover { color: #e9edf4; background: #ffffff0d; }

h2 {
  margin: 14px 0 2px;
  font-size: 22px;
  font-weight: 500;
  letter-spacing: -0.03em;
  color: var(--light);
}

.tagline { margin: 0 0 12px; font-size: 13px; color: #d5ddec; }
.description { margin: 0 0 16px; font-size: 12.5px; line-height: 1.65; color: #a1aabd; }

.payload-label {
  margin: 0 0 8px;
  font: 10px/1 ui-monospace, "SF Mono", Menlo, monospace;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: #6f7c93;
}

.payload {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
  margin: 0 0 20px;
  padding: 0;
  list-style: none;
}

.payload li {
  padding: 3px 9px;
  border: 1px solid #a9c1e21f;
  border-radius: 999px;
  font-size: 11px;
  color: #c3ccdb;
}

.actions { display: flex; gap: 8px; flex-wrap: wrap; }

.action {
  display: inline-flex;
  align-items: center;
  gap: 7px;
  padding: 8px 14px;
  border: 1px solid #a9c1e22a;
  border-radius: 999px;
  font-size: 12px;
  color: #d5ddec;
  transition: background 200ms, border-color 200ms;
}

.action img { filter: brightness(0) invert(1); opacity: 0.8; }
.action span { color: #8c9bb4; }
.action:hover { background: #ffffff0a; border-color: #a9c1e244; }

.action.primary {
  border-color: transparent;
  background: color-mix(in srgb, var(--color) 55%, #0a0e16);
  color: #fff;
}

.action.primary span { color: color-mix(in srgb, var(--light) 80%, #fff); }
.action.primary:hover { background: color-mix(in srgb, var(--color) 75%, #0a0e16); }

.log-enter-active, .log-leave-active { transition: opacity 220ms, transform 220ms; }
.log-enter-from, .log-leave-to { opacity: 0; transform: translateY(8px); }

@media (max-width: 600px) {
  .mission-log,
  .mission-log.left,
  .mission-log.right {
    position: fixed;
    top: auto;
    left: 12px;
    right: 12px;
    bottom: 68px; /* clear the skills ticker */
    width: auto;
    translate: none;
  }
}
</style>
