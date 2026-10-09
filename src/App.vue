<template>
  <div class="portfolio" :style="{ '--origin': origin }">
    <CustomCursor v-if="!isMobile" />
    <StarBackground :warp="warping" />
    <NavigationPlanet v-if="!isMobile" />
    <Transition :name="direction" mode="out-in" @before-leave="warping = true" @after-enter="arrive">
      <main v-if="view === 'home'" key="home" class="content" aria-label="Tommaso Lo Piparo's portfolio">
        <Rocket @launch="element => travel('projects', element)" />
        <Typing />
        <SolarSystem :planets="planets" label="Explore my solar system">
          <Portrait />
        </SolarSystem>
      </main>
      <main v-else :key="view" class="content" :aria-label="headers[view].label">
        <button type="button" class="back" @click="go(view === 'projects' ? 'home' : 'projects')">
          <span aria-hidden="true">←</span> {{ view === "projects" ? "Home system" : "Projects" }}
        </button>
        <header class="text-container">
          <p class="eyebrow">{{ headers[view].eyebrow }}</p>
          <h1 ref="title" tabindex="-1">{{ headers[view].title }} <span class="highlight">{{ headers[view].highlight }}</span></h1>
          <p class="description">{{ headers[view].description }}</p>
        </header>
        <ProjectsSystem v-if="view === 'projects'" @enter="travel"><Portrait /></ProjectsSystem>
        <ProjectsSystem v-else :group="view" />
      </main>
    </Transition>
    <SkillsTicker />
  </div>
</template>

<script setup lang="ts">
import { onMounted, onUnmounted, ref, watch } from "vue";
import { isMobile as useMobile } from "./composables/useMobile.ts";
import StarBackground from "./components/StarBackground.vue";
import SolarSystem, { type Planet } from "./components/SolarSystem.vue";
import ProjectsSystem from "./components/ProjectsSystem.vue";
import Portrait from "./components/Portrait.vue";
import SkillsTicker from "./components/SkillsTicker.vue";
import Typing from "./components/Typing.vue";
import NavigationPlanet from "./components/NavigationPlanet.vue";
import Rocket from "./components/Rocket.vue";
import CustomCursor from "./components/CustomCursor.vue";

const { isMobile } = useMobile();

const planets: Planet[] = [
  { name: "LinkedIn", href: "https://www.linkedin.com/in/tommasolopiparo", logo: "linkedin.svg", radius: 0.205, period: 24, angle: 3.9, size: 56, light: "#8ac4e6", color: "#236996", shadow: "#091829" },
  { name: "GitHub", href: "https://github.com/pippobaudoicon", logo: "github.svg", radius: 0.285, period: 34, angle: 0.7, size: 74, light: "#c4b3ed", color: "#69568d", shadow: "#1b142c" },
  { name: "Instagram", href: "https://www.instagram.com/tommilopi", logo: "instagram.svg", radius: 0.365, period: 46, angle: 2.8, size: 61, light: "#e6a7ba", color: "#974864", shadow: "#2a111f" },
  { name: "Contact me", href: "mailto:tommaso.lopiparo@gmail.com", logo: "email.svg", radius: 0.445, period: 60, angle: 5.6, size: 67, light: "#edb99c", color: "#ac6550", shadow: "#301a17" },
];

const headers = {
  projects: {
    label: "My projects",
    eyebrow: "System 02 · Side projects",
    title: "Things I build",
    highlight: "for fun.",
    description: "Side projects from my own time. My 6+ years of professional work mostly lives in private company repos, so it isn’t here. Fly into a group, or open a planet’s mission log.",
  },
  games: {
    label: "My games",
    eyebrow: "System 03 · Games",
    title: "Games, for the",
    highlight: "fun of it.",
    description: "Made in my free time. Every planet here is live, with its code in the open. Pick one to read its mission log.",
  },
};

type View = "home" | keyof typeof headers;
const depth = (view: View) => (view === "home" ? 0 : view === "projects" ? 1 : 2);
const hashes: Record<View, string> = { home: "", projects: "#projects", games: "#projects/games" };
const viewFromHash = () => (Object.keys(hashes) as View[]).find(view => hashes[view] === location.hash) ?? "home";

const view = ref<View>(viewFromHash());
const warping = ref(false);
const direction = ref<"warp-in" | "warp-out">("warp-in");
// Where you clicked to enter each system: going in zooms into that spot, coming back shrinks into it.
const origins: Partial<Record<View, string>> = {};
const origin = ref("50% 50%");
const title = ref<HTMLElement | null>(null);

watch(view, (next, previous) => {
  const deeper = depth(next) > depth(previous);
  direction.value = deeper ? "warp-in" : "warp-out";
  origin.value = origins[deeper ? next : previous] ?? "50% 50%";
});

function go(next: View) {
  history.pushState(null, "", hashes[next] || location.pathname + location.search);
  view.value = next;
}

function travel(next: View, element: HTMLElement) {
  const rect = element.getBoundingClientRect();
  origins[next] = `${rect.left + rect.width / 2}px ${rect.top + rect.height / 2}px`;
  go(next);
}

function arrive() {
  warping.value = false;
  if (view.value !== "home") title.value?.focus({ preventScroll: true });
}

const onPopState = () => (view.value = viewFromHash());
onMounted(() => window.addEventListener("popstate", onPopState));
onUnmounted(() => window.removeEventListener("popstate", onPopState));
</script>

<style scoped>
.portfolio {
  position: relative;
  min-height: 100svh;
  display: flex;
  flex-direction: column;
  overflow: clip;
  background:
    radial-gradient(ellipse at 50% 53%, #15203155 0%, #090c1300 52%),
    #07090e;
  color: #e9edf4;
}

.content {
  position: relative;
  transform-origin: var(--origin);
  z-index: 1;
  min-height: calc(100svh - 56px);
  padding: 42px 0 12px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 8px;
}

/* Jumping between systems: fly into what you clicked, the new system grows out of it. */
.warp-in-leave-active, .warp-out-leave-active { transition: transform 600ms cubic-bezier(0.6, 0, 0.9, 0.4), opacity 600ms ease-in, filter 600ms; }
.warp-in-enter-active, .warp-out-enter-active { transition: transform 750ms cubic-bezier(0.15, 0.7, 0.3, 1), opacity 750ms ease-out, filter 750ms; }
.warp-in-leave-to, .warp-out-enter-from { transform: scale(3.2); opacity: 0; filter: blur(8px); }
.warp-in-enter-from, .warp-out-leave-to { transform: scale(0.12); opacity: 0; filter: blur(4px); }

.back {
  position: absolute;
  top: 32px;
  left: 28px;
  padding: 8px 14px;
  border: 1px solid #a9c1e21f;
  border-radius: 999px;
  background: #080b11b0;
  color: #a1aabd;
  font-size: 12px;
  cursor: pointer;
  z-index: 100;
  transition: color 200ms, border-color 200ms;
}

.back:hover { color: #e9edf4; border-color: #a9c1e244; }

.text-container {
  position: relative;
  width: min(980px, 92vw);
  text-align: center;
  z-index: 40;
}

.eyebrow {
  margin: 0 0 10px;
  font: 10px/1 ui-monospace, "SF Mono", Menlo, monospace;
  letter-spacing: 0.18em;
  text-transform: uppercase;
  color: #6f7c93;
}

h1 {
  margin: 0 0 14px;
  font-size: clamp(26px, 3.1vw, 40px);
  line-height: 1.25;
  font-weight: 500;
  letter-spacing: -0.045em;
}

h1:focus { outline: none; }
.highlight { color: #bbd8cd; }

.description {
  max-width: 720px;
  margin: 0 auto;
  text-wrap: balance;
  font-size: 13px;
  line-height: 1.7;
  color: #a1aabd;
}

@media (max-width: 600px) {
  .back { top: 14px; left: 14px; padding: 6px 12px; font-size: 11px; }
  .text-container { padding-top: 22px; }
  h1 { font-size: clamp(24px, 6vw, 32px); }
  .description { font-size: 12px; }
}

@media (max-width: 600px) {
  .content {
    padding: 32px 0 18px;
    gap: 12px;
  }
}

@media (max-height: 550px) and (min-width: 601px) {
  .content {
    padding-top: 20px;
  }
}

@media (max-width: 600px) and (max-height: 650px) {
  .content {
    padding: 24px 0 12px;
    gap: 12px;
  }
}
</style>
