<template>
  <div class="portfolio">
    <StarBackground  />
    <NavigationPlanet v-if="!isMobile" />
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
  import NavigationPlanet from "./components/NavigationPlanet.vue";

  import { ref, onMounted, onUnmounted } from 'vue';
  
  const isMobile = ref(false);
  
  const checkIfMobile = () => {
    isMobile.value = window.innerWidth <= 768;
  };
  
  onMounted(() => {
    checkIfMobile();
    window.addEventListener('resize', checkIfMobile);
  });
  
  onUnmounted(() => {
    window.removeEventListener('resize', checkIfMobile);
  });
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
