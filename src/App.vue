<template>
  <div class="portfolio">
    <StarBackground />
    <div class="content">
      <Typing />
      <SolarSystem />
    </div>
    <SkillsTicker />
  </div>
</template>

<script setup lang="ts">
  import StarBackground from "./components/StarBackground.vue";
  import SolarSystem from "./components/SolarSystem.vue";
  import SkillsTicker from "./components/SkillsTicker.vue";
  import Typing from "./components/Typing.vue";
</script>

<style scoped>
.portfolio {
  position: relative;
  width: 100vw;
  height: 100vh;
  background-color: #0a0a0a;
  color: #ffffff;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  overflow: hidden;
}

.content {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>
