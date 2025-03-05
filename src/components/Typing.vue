<template>
    <div class="text-container">
        <h1 v-show="showText">{{ description }}</h1>
    </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";

const description = ref("");
const fullText = "Hi, I'm Tommaso Lo Piparo. A passionate developer skilled in Vue, Node.js, Docker, and more.";
const typingSpeed = 50;
const showText = ref(false);

const typeText = async () => {
    showText.value = true;
    for (let i = 0; i < fullText.length; i++) {
        await new Promise(resolve => setTimeout(resolve, typingSpeed));
        description.value += fullText[i];
    }
};

onMounted(() => {
    setTimeout(typeText, 500); // Delay before typing starts
});
</script>

<style scoped>
.text-container {
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
}

/* 📝 Text animation */
h1 {
  font-size: 1.8rem;
  font-weight: 600;
  opacity: 0;
  transform: translateY(10px);
  animation: 
    fadeIn 1.5s forwards 0.5s,
    float 4s ease-in-out infinite;
  transform-origin: center;
  text-shadow: 0 0 5px rgba(255, 255, 255, 0.3);
  letter-spacing: 1px;
}

@keyframes fadeIn {
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes float {
  0% {
    transform: translateY(0px);
  }
  50% {
    transform: translateY(-10px);
  }
  100% {
    transform: translateY(0px);
  }
}

h1::after {
  content: "|";
  display: inline-block;
  animation: blink 0.7s infinite;
}

@keyframes blink {
  50% {
    opacity: 0;
  }
}
</style>