<template>
  <div class="solar-system" ref="solarSystem">
    <!-- LinkedIn -->
    <a href="https://www.linkedin.com/in/your-linkedin" 
       target="_blank"
       class="planet planet-1" 
       @mouseenter="showTooltip($event, 'LinkedIn')" 
       @mouseleave="hideTooltip">
      <img src="/logos/linkedin.svg" alt="LinkedIn" class="planet-icon">
    </a>
    <!-- GitHub -->
    <a href="https://github.com/your-github" 
       target="_blank"
       class="planet planet-2" 
       @mouseenter="showTooltip($event, 'GitHub')" 
       @mouseleave="hideTooltip">
      <img src="/logos/github.svg" alt="GitHub" class="planet-icon">
    </a>
    <!-- Instagram -->
    <a href="https://www.instagram.com/your-instagram" 
       target="_blank"
       class="planet planet-3" 
       @mouseenter="showTooltip($event, 'Instagram')" 
       @mouseleave="hideTooltip">
      <img src="/logos/instagram.svg" alt="Instagram" class="planet-icon">
    </a>
    <!-- Contact -->
    <a href="mailto:your.email@example.com" 
       class="planet planet-4" 
       @mouseenter="showTooltip($event, 'Contact Me')" 
       @mouseleave="hideTooltip">
      <img src="/logos/email.svg" alt="Email" class="planet-icon">
    </a>
    
    <div class="image-container">
      <div class="backdrop"></div>
      <a href="resume" 
         @mouseenter="showTooltip($event, 'View Resume', 200)" 
         @mouseleave="hideTooltip">
        <img src="/Tom.jpeg" alt="Tommaso Lo Piparo" class="face" />
      </a>
    </div>

    <!-- Tooltip element, positioned absolutely within the solar system container -->
    <div v-if="tooltip.visible" 
         class="tooltip" 
         :style="{ top: tooltip.top + 'px', left: tooltip.left + 'px' }">
      {{ tooltip.text }}
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tooltip: {
        visible: false,
        text: '',
        top: 0,
        left: 0,
      },
    }
  },
  methods: {
    showTooltip(event, text, offset = 0) {
      // Get the planet's position relative to the viewport
      const planetRect = event.currentTarget.getBoundingClientRect();
      // Get the solar system container's position relative to the viewport
      const containerRect = this.$refs.solarSystem.getBoundingClientRect();
      
      // Calculate the tooltip's position relative to the container:
      this.tooltip.top = planetRect.top - containerRect.top - 5 - offset;
      this.tooltip.left = planetRect.left - containerRect.left + planetRect.width / 2;
      
      this.tooltip.text = text;
      this.tooltip.visible = true;
    },
    hideTooltip() {
      this.tooltip.visible = false;
    },
  },
};
</script>


<style scoped>
.solar-system {
  position: relative;
  width: 800px;
  height: 800px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.planet {
  position: absolute;
  border-radius: 50%;
  pointer-events: auto;
  cursor: pointer;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  transform-origin: center center;
  z-index: 999;
}

.planet:hover {
  animation-play-state: paused;
  box-shadow: 0 0 15px rgba(255, 255, 255, 0.5);
  z-index: 999;
}

.planet-1 {
  width: 60px;
  height: 60px;
  background: radial-gradient(circle at 30% 30%, #0077b5, #004d73); /* LinkedIn colors */
  animation: orbit1 8s linear infinite;
}

.planet-2 {
  width: 90px;
  height: 90px;
  background: radial-gradient(circle at 30% 30%, #6e5494, #4b367c); /* GitHub colors */
  animation: orbit2 12s linear infinite;
}

.planet-3 {
  width: 70px;
  height: 70px;
  background: radial-gradient(circle at 30% 30%, #E4405F, #833AB4); /* Instagram colors */
  animation: orbit3 15s linear infinite;
}

.planet-4 {
  width: 80px;
  height: 80px;
  background: radial-gradient(circle at 30% 30%, #ff4f4f, #cc0000); /* Email/Contact colors */
  animation: orbit4 20s linear infinite;
}

.image-container {
  position: relative;
  padding: 1rem;
}

.backdrop {
  position: absolute;
  inset: 0;
  background: radial-gradient(circle at center, #2c3e50 0%, #1a1a1a 100%);
  filter: blur(20px);
  opacity: 0.7;
  border-radius: 50%;
  transform: scale(0.9);
}

.face {
  position: relative;
  width: 200px;
  height: 200px;
  border-radius: 50%;
  object-fit: cover;
  filter: grayscale(80%) contrast(1.1);
  box-shadow: 0 0 25px rgba(0, 0, 0, 0.3),
              0 0 45px rgba(0, 100, 255, 0.1);
  transition: all 0.3s ease;
  animation: float 4s ease-in-out infinite;
  z-index: 10;
}

.face:hover {
  filter: grayscale(30%) contrast(1.1);
  transform: scale(1.5);
  box-shadow: 0 0 30px rgba(0, 100, 255, 0.3);
}

@keyframes float {
  0%, 100% { transform: translateY(0) rotate(0deg); }
  25% { transform: translateY(-8px) rotate(2deg); }
  75% { transform: translateY(8px) rotate(-2deg); }
}

@keyframes orbit1 {
  from { transform: rotate(0deg) translateX(160px) scale(1); }
  to   { transform: rotate(360deg) translateX(160px) scale(1); }
}

@keyframes orbit2 {
  from { transform: rotate(0deg) translateX(240px) scale(1); }
  to   { transform: rotate(360deg) translateX(240px) scale(1); }
}

@keyframes orbit3 {
  from { transform: rotate(0deg) translateX(320px) scale(1); }
  to   { transform: rotate(360deg) translateX(320px) scale(1); }
}

@keyframes orbit4 {
  from { transform: rotate(0deg) translateX(390px) scale(1); }
  to   { transform: rotate(360deg) translateX(390px) scale(1); }
}

.solar-system {
  position: relative;
  width: 600px;
  height: 600px;
  display: flex;
  justify-content: center;
  align-items: center;
}

/* Planet styling (now applied to anchor tags) */
.planet {
  position: absolute;
  border-radius: 50%;
  pointer-events: auto;
  cursor: pointer;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  transform-origin: center center;
  z-index: 1;
  /* Ensure anchor behaves as a block-level element for dimensions */
  display: block;
}

/* Tooltip styling using the data-tooltip attribute */
.planet::after {
  content: attr(data-tooltip);
  position: absolute;
  top: -35px; /* Adjust as needed */
  left: 50%;
  transform: translateX(-50%);
  background: rgba(0, 0, 0, 0.7);
  color: #fff;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 12px;
  white-space: nowrap;
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.3s ease;
}

.planet:hover::after {
  opacity: 1;
}

.tooltip {
  position: absolute;
  transform: translate(-50%, -100%);
  background: rgba(0, 0, 0, 0.7);
  color: #fff;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 12px;
  white-space: nowrap;
  pointer-events: none;
}

.planet-icon {
  width: 60%;
  height: 60%;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  filter: brightness(0) invert(1);
  opacity: 0.8;
  transition: opacity 0.3s ease;
}

.planet:hover .planet-icon {
  opacity: 1;
}
</style>
