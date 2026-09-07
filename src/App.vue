<template>
  <div class="portfolio">
    <CustomCursor v-if="!isMobile" />
    <StarBackground />
    <NavigationPlanet v-if="!isMobile" />
    <main class="content" aria-label="Tommaso Lo Piparo's portfolio">
      <Typing />
      <SolarSystem />
    </main>
    <SkillsTicker />
  </div>
</template>

<script setup lang="ts">
import { isMobile as useMobile } from "./composables/useMobile.ts";
import StarBackground from "./components/StarBackground.vue";
import SolarSystem from "./components/SolarSystem.vue";
import SkillsTicker from "./components/SkillsTicker.vue";
import Typing from "./components/Typing.vue";
import NavigationPlanet from "./components/NavigationPlanet.vue";
import CustomCursor from "./components/CustomCursor.vue";

const { isMobile } = useMobile();
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
  z-index: 1;
  min-height: calc(100svh - 56px);
  padding: 42px 0 12px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 8px;
}

@media (max-width: 600px) {
  .content {
    padding: 44px 0 28px;
    gap: 24px;
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
