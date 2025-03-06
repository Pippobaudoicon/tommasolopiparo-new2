<template>
  <div class="solar-system" ref="solarSystem">
    <!-- LinkedIn -->
    <a href="https://www.linkedin.com/in/tommasolopiparo" 
       target="_blank"
       class="planet-container planet-container-1" 
       @mouseenter="showTooltip($event, 'LinkedIn')" 
       @mouseleave="hideTooltip">
      <div class="planet planet-1">
        <div class="planet-surface"></div>
        <img src="/logos/linkedin.svg" target="_blank" alt="LinkedIn" class="planet-icon">
      </div>
      <div class="ring ring-outer ring-1"></div>
      <div class="ring ring-inner ring-1"></div>
    </a>
    
    <!-- GitHub -->
    <a href="https://github.com/pippobaudoicon" 
       target="_blank"
       class="planet-container planet-container-2" 
       @mouseenter="showTooltip($event, 'GitHub')" 
       @mouseleave="hideTooltip">
      <div class="planet planet-2">
        <div class="planet-surface"></div>
        <img src="/logos/github.svg" target="_blank" alt="GitHub" class="planet-icon">
      </div>
      <div class="ring ring-outer ring-2"></div>
      <div class="ring ring-inner ring-2"></div>
    </a>
    
    <!-- Instagram -->
    <a href="https://www.instagram.com/tommilopi" 
       target="_blank"
       class="planet-container planet-container-3" 
       @mouseenter="showTooltip($event, 'Instagram')" 
       @mouseleave="hideTooltip">
      <div class="planet planet-3">
        <div class="planet-surface"></div>
        <img src="/logos/instagram.svg" target="_blank" alt="Instagram" class="planet-icon">
      </div>
      <div class="ring ring-outer ring-3"></div>
      <div class="ring ring-inner ring-3"></div>
    </a>
    
    <!-- Contact -->
    <a href="mailto:tommaso.lopiparo@gmail.com" 
       class="planet-container planet-container-4" 
       @mouseenter="showTooltip($event, 'Contact Me')" 
       @mouseleave="hideTooltip">
      <div class="planet planet-4">
        <div class="planet-surface"></div>
        <img src="/logos/email.svg" alt="Email" class="planet-icon">
      </div>
      <div class="ring ring-outer ring-4"></div>
      <div class="ring ring-inner ring-4"></div>
    </a>
    
    <div class="image-container">
      <div class="backdrop"></div>
      <a href="/Lo Piparo CV.pdf" 
         @mouseenter="showTooltip($event, 'View Resume', 200)" 
         @mouseleave="hideTooltip">
        <img src="/Tom.jpeg" target="_blank" alt="Tommaso Lo Piparo" class="face" />
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
  width: 600px;
  height: 600px;
  display: flex;
  justify-content: center;
  align-items: center;
  perspective: 1000px;
  transform-style: preserve-3d;
  z-index: 10;
}

.planet-container {
  position: absolute;
  display: flex;
  justify-content: center;
  align-items: center;
  transform-style: preserve-3d;
  cursor: pointer;
  z-index: 20;
}

.planet-container-1 {
  width: 90px;
  height: 90px;
  animation: orbit1 8s linear infinite;
  animation-delay: -3.2s;
}

.planet-container-2 {
  width: 120px;
  height: 120px;
  animation: orbit2 12s linear infinite;
  animation-delay: -7.1s;
}

.planet-container-3 {
  width: 100px;
  height: 100px;
  animation: orbit3 15s linear infinite;
  animation-delay: -2.1s;
}

.planet-container-4 {
  width: 110px;
  height: 110px;
  animation: orbit4 20s linear infinite;
  animation-delay: -5.2s;
}

.planet {
  position: absolute;
  border-radius: 50%;
  pointer-events: auto;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  transform-style: preserve-3d;
  z-index: 30;
  overflow: hidden;
}

.planet-surface {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  animation: rotate 20s linear infinite;
  background-image: 
    radial-gradient(circle at 10% 40%, rgba(255, 255, 255, 0.1) 5%, transparent 8%),
    radial-gradient(circle at 80% 30%, rgba(255, 255, 255, 0.1) 6%, transparent 9%),
    radial-gradient(circle at 40% 70%, rgba(255, 255, 255, 0.1) 4%, transparent 7%),
    radial-gradient(circle at 60% 20%, rgba(255, 255, 255, 0.1) 3%, transparent 6%);
}

.planet-container:hover {
  animation-play-state: paused;
  z-index: 999;
}

.planet-container:hover .planet {
  transform: scale(1.15);
  box-shadow: 0 0 25px rgba(255, 255, 255, 0.6);
}

.planet-1 {
  width: 60px;
  height: 60px;
  background: radial-gradient(circle at 30% 30%, #0077b5, #004d73); /* LinkedIn colors */
  box-shadow: 0 0 15px rgba(0, 119, 181, 0.5), 0 0 30px rgba(0, 119, 181, 0.2);
}

.planet-2 {
  width: 90px;
  height: 90px;
  background: radial-gradient(circle at 30% 30%, #6e5494, #4b367c); /* GitHub colors */
  box-shadow: 0 0 15px rgba(110, 84, 148, 0.5), 0 0 30px rgba(110, 84, 148, 0.2);
}

.planet-3 {
  width: 70px;
  height: 70px;
  background: radial-gradient(circle at 30% 30%, #E4405F, #833AB4); /* Instagram colors */
  box-shadow: 0 0 15px rgba(228, 64, 95, 0.5), 0 0 30px rgba(131, 58, 180, 0.2);
}

.planet-4 {
  width: 80px;
  height: 80px;
  background: radial-gradient(circle at 30% 30%, #ff4f4f, #cc0000); /* Email/Contact colors */
  box-shadow: 0 0 15px rgba(255, 79, 79, 0.5), 0 0 30px rgba(204, 0, 0, 0.2);
}

/* Ring styles */
.ring {
  position: absolute;
  border-radius: 50%;
  border-style: solid;
  border-width: 1px;
  transform: rotateX(75deg);
  transform-style: preserve-3d;
  pointer-events: none;
}

.ring-outer {
  border-width: 2px;
}

.ring-inner {
  border-width: 1px;
}

/* LinkedIn rings */
.ring-1.ring-outer {
  width: 80px;
  height: 80px;
  border-color: rgba(0, 119, 181, 0.5);
  animation: ring-rotate 8s linear infinite;
}

.ring-1.ring-inner {
  width: 70px;
  height: 70px;
  border-color: rgba(0, 119, 181, 0.3);
  animation: ring-rotate-reverse 6s linear infinite;
}

/* GitHub rings */
.ring-2.ring-outer {
  width: 110px;
  height: 110px;
  border-color: rgba(110, 84, 148, 0.5);
  animation: ring-rotate 10s linear infinite;
}

.ring-2.ring-inner {
  width: 100px;
  height: 100px;
  border-color: rgba(110, 84, 148, 0.3);
  animation: ring-rotate-reverse 7s linear infinite;
}

/* Instagram rings */
.ring-3.ring-outer {
  width: 90px;
  height: 90px;
  border-color: rgba(228, 64, 95, 0.5);
  animation: ring-rotate 9s linear infinite;
}

.ring-3.ring-inner {
  width: 80px;
  height: 80px;
  border-color: rgba(131, 58, 180, 0.3);
  animation: ring-rotate-reverse 5s linear infinite;
}

.ring-4.ring-outer {
  width: 100px;
  height: 100px;
  border-color: rgba(255, 79, 79, 0.5);
  animation: ring-rotate 11s linear infinite;
}

.ring-4.ring-inner {
  width: 90px;
  height: 90px;
  border-color: rgba(255, 79, 79, 0.3);
  animation: ring-rotate-reverse 8s linear infinite;
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
  z-index: 50;
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
  from { transform: rotate(0deg) translateX(250px) scale(1); }
  to   { transform: rotate(360deg) translateX(250px) scale(1); }
}

@keyframes orbit3 {
  from { transform: rotate(0deg) translateX(330px) scale(1); }
  to   { transform: rotate(360deg) translateX(330px) scale(1); }
}

@keyframes orbit4 {
  from { transform: rotate(0deg) translateX(420px) scale(1); }
  to   { transform: rotate(360deg) translateX(420px) scale(1); }
}

@keyframes ring-rotate {
  from { transform: rotateX(75deg) rotate(0deg); }
  to { transform: rotateX(75deg) rotate(360deg); }
}

@keyframes ring-rotate-reverse {
  from { transform: rotateX(75deg) rotate(360deg); }
  to { transform: rotateX(75deg) rotate(0deg); }
}

@keyframes rotate {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
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
  z-index: 40;
}

.planet-container:hover .planet-icon {
  opacity: 1;
}
</style>
