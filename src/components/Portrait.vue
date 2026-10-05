<template>
  <a class="image-container" href="/Lo%20Piparo%20CV.pdf" target="_blank" rel="noopener noreferrer" aria-label="View Tommaso Lo Piparo's résumé">
    <span class="solar-corona" aria-hidden="true" />
    <img src="/Tom.webp" alt="Tommaso Lo Piparo" class="face" fetchpriority="high" width="200" height="200" decoding="async" />
    <span class="resume-label" aria-hidden="true">View résumé ↗</span>
  </a>
</template>

<style scoped>
.image-container {
  position: absolute;
  inset: 0;
  display: block;
  border-radius: 50%;
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

.image-container:is(:hover, :focus-visible) .resume-label {
  opacity: 1;
  transform: translate(-50%, 0);
}

@media (max-width: 600px) {
  .resume-label { font-size: 10px; }
}
</style>
