<script setup lang="ts">
/// <reference lib="es2022.intl" />
import { onMounted, onUnmounted, ref } from "vue";

const sentences = [
    " Software Engineer focused on Back-End by day 🌞 👨‍💻, problem-solving ninja by night. 🌙 🥷",
    " Expert in Node.js, Laravel, and Flask, with strong proficiency in their respective languages, and fixing last-minute bugs. 🐛🔧",
    " Turning swearings into APIs and database structures. 🤬 ➡️ 💻",
    " Git guru, networking nerd, and database whisperer. 🧙‍♂️ 🔌",
    " I write code that works on the first try... that's what I like to think at least! 😅",
    " I break problems, not production servers (well, almost never). 🤞 💪",
    " Team player who actually enjoys code reviews (weird, right?). 🤓 👍",
    " Passionate about coding, teaching, and finding the perfect GIF for every occasion. 💻 👨‍🏫 🎭",
    " Dreaming of leading a dev team—until then, I'll settle for debugging everything. 🔍 🐞",
    " Tech enthusiast, stock market watcher, and part-time movie critic. 🎬 💻 📈",
    " I'm always up for a challenge, so let's build something awesome together! 🛠️ 🚀",
    " I'm currently looking for new opportunities, so feel free to reach out! 📧 🤝",
    " Thanks for visiting my portfolio! ☄️ 🚀",
].map(sentence => sentence.trim());
const text = ref(sentences[0]);
let cleanup = () => {};

onMounted(() => {
  const motion = window.matchMedia("(prefers-reduced-motion: reduce)");
  let timeout: ReturnType<typeof setTimeout>;
  let sentenceIndex = 0;
  let deleting = true;
  // Segment complete graphemes so emoji sequences are typed and erased together.
  const segmenter = typeof Intl.Segmenter === "function"
    ? new Intl.Segmenter("en", { granularity: "grapheme" })
    : null;
  const characters = (value: string) => segmenter
    ? Array.from(segmenter.segment(value), part => part.segment)
    : Array.from(value);

  function tick() {
    const target = characters(sentences[sentenceIndex]);
    const current = characters(text.value);
    if (deleting) {
      text.value = current.slice(0, -1).join("");
      if (!text.value) {
        deleting = false;
        sentenceIndex = (sentenceIndex + 1) % sentences.length;
      }
      timeout = setTimeout(tick, text.value ? 24 : 400);
    } else {
      text.value = target.slice(0, current.length + 1).join("");
      deleting = text.value === sentences[sentenceIndex];
      timeout = setTimeout(tick, deleting ? 4500 : 38);
    }
  }

  function syncTyping() {
    clearTimeout(timeout);
    text.value = sentences[0];
    sentenceIndex = 0;
    deleting = true;
    if (!motion.matches && !document.hidden) timeout = setTimeout(tick, 4500);
  }

  motion.addEventListener("change", syncTyping);
  document.addEventListener("visibilitychange", syncTyping);
  syncTyping();
  cleanup = () => {
    clearTimeout(timeout);
    motion.removeEventListener("change", syncTyping);
    document.removeEventListener("visibilitychange", syncTyping);
  };
});

onUnmounted(() => cleanup());
</script>

<template>
  <header class="text-container">
    <h1><span class="greeting">Hi, I’m</span> <span class="highlight-name">Tommaso Lo Piparo.</span></h1>
    <p class="description" aria-hidden="true">{{ text }}<span class="cursor">|</span></p>
    <p class="sr-only">Software engineer focused on backend development, APIs, and databases.</p>
  </header>
</template>

<style scoped>
.text-container {
  position: relative;
  width: min(720px, 86vw);
  text-align: center;
  z-index: 40;
}

h1 {
  margin: 0 0 14px;
  font-size: clamp(26px, 3.1vw, 40px);
  line-height: 1.25;
  font-weight: 500;
  letter-spacing: -0.045em;
}

.greeting { font-weight: 400; color: #a4adbd; }
.highlight-name { color: #bbd8cd; }

.description {
  height: 3.4em;
  margin: 0 auto;
  max-width: 660px;
  font-size: 13px;
  line-height: 1.7;
  font-weight: 400;
  color: #a1aabd;
  text-wrap: balance;
}

.cursor {
  margin-left: 3px;
  color: #9ab6bc;
  animation: blink 1s step-end infinite;
}

@keyframes blink { 50% { opacity: 0; } }

@media (max-width: 600px) {
  h1 { font-size: clamp(24px, 6vw, 32px); margin-bottom: 15px; }
  .greeting { display: block; font-size: 14px; letter-spacing: 0; margin-bottom: 8px; }
  .description { font-size: 12px; height: 6.8em; }
}

@media (prefers-reduced-motion: reduce) {
  .cursor { display: none; }
}
</style>
