<template>
  <div class="planet-container">
    <a :href="href" :aria-label="orbitText" class="planet-link" target="_blank" rel="noopener noreferrer">
      <div class="planet-orbit">
        <div class="planet">
          <div class="planet-surface"></div>
        </div>
        <div class="ring ring-1"></div>
        <div class="ring ring-2"></div>
        <div class="text-container">
          <div class="planet-text">{{ orbitText }}</div>
        </div>
      </div>
    </a>
  </div>
</template>

<script>
export default {
  name: 'NavigationPlanet',
  props: {
    href: {
      type: String,
      default: 'https://old.tommasolopiparo.com'
    },
    orbitText: {
      type: String,
      default: 'OG Website'
    }
  }
}
</script>

<style scoped>
.planet-container {
  position: absolute;
  width: 70px;
  height: 70px;
  top: 24px;
  right: 28px;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 100;
}

.planet-link {
  text-decoration: none;
  color: white;
  width: 100%;
  height: 100%;
  display: block;
}

.planet-orbit {
  position: relative;
  width: 70px;
  height: 70px;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  transform-style: preserve-3d;
  perspective: 800px;
  transition: transform 0.5s ease;
}

.planet {
  position: absolute;
  width: 35px;
  height: 35px;
  background: radial-gradient(circle at 28% 23%, #a6c8d8, #49798e 37%, #101f32 78%);
  border-radius: 50%;
  box-shadow: 
    inset 0 0 20px rgba(0, 0, 0, 0.5),
    0 0 20px rgba(81, 186, 253, 0.12);
  overflow: hidden;
  z-index: 2;
}

.planet-surface {
  position: absolute;
  width: 100%;
  height: 100%;
  background-image: 
    radial-gradient(circle at 10% 40%, rgba(255, 255, 255, 0.2) 5%, transparent 8%),
    radial-gradient(circle at 80% 30%, rgba(255, 255, 255, 0.2) 6%, transparent 9%),
    radial-gradient(circle at 40% 70%, rgba(255, 255, 255, 0.3) 4%, transparent 7%),
    radial-gradient(circle at 60% 20%, rgba(255, 255, 255, 0.3) 3%, transparent 6%);
  animation: rotate 20s linear infinite;
}

.ring {
  position: absolute;
  border-radius: 50%;
  border-style: solid;
  border-width: 1px;
  transform: rotateX(75deg);
  transform-style: preserve-3d;
}

.ring-1 {
  width: 62px;
  height: 62px;
  border-color: rgba(160, 212, 235, 0.35);
  animation: ring-rotate 10s linear infinite;
}

.ring-2 {
  width: 54px;
  height: 54px;
  border-color: rgba(116, 164, 185, 0.2);
  animation: ring-rotate-reverse 8s linear infinite;
}

.text-container {
  position: absolute;
  bottom: -12px;
  transform: translateY(0);
  transition: transform 0.3s ease;
  z-index: 3;
}

.planet-text {
  color: #a1aabd;
  font-size: 10px;
  font-weight: 400;
  opacity: 0;
  transition: opacity 0.3s ease;
  white-space: nowrap;
}

.planet-link:is(:hover, :focus-visible) .planet-orbit {
  transform: translateY(-3px);
}

.planet-link:hover .planet {
  box-shadow: 
    inset 0 0 20px rgba(0, 0, 0, 0.5),
    0 0 30px rgba(81, 186, 253, 0.7);
}

.planet-link:is(:hover, :focus-visible) .planet-text {
  opacity: 1;
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
</style>
