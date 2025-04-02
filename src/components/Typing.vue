<template>
    <div class="text-container">
        <h1 v-show="showText">
            <span v-for="(segment, index) in renderedSegments" :key="index" :class="segment.class">
                {{ segment.text }}
            </span>
            <span class="cursor" v-if="isTyping">|</span>
        </h1>
    </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";

interface TextSegment {
    text: string;
    class?: string;
    style?: Record<string, string>;
    permanent?: boolean;
}

const permanentSegments = ref<TextSegment[]>([
    { text: "Hi, I'm ", permanent: true },
    { text: "Tommaso Lo Piparo.", class: "highlight-name", permanent: true },
]);

const changingSegments = ref<string[]>([
    " Back-end and DevOps by day 🌞 👨‍💻, problem-solving ninja by night. 🌙 🥷",
    " Fluent in PHP, Js/Ts, Python, various Frameworks and fixing last-minute bugs. 🐛 🔧",
    " I write code that works on the first try... that's what I like to think at least! 😅",
    " Turning swearings into APIs and database structures. 🤬 ➡️ 💻",
    " I break problems, not production servers (well, almost never). 🤞 💪",
    " Git guru, networking nerd, and database whisperer. 🧙‍♂️ 🔌",
    " Team player who actually enjoys code reviews (weird, right?). 🤓 👍",
    " Passionate about coding, teaching, and finding the perfect GIF for every occasion. 💻 👨‍🏫 🎭",
    " Dreaming of leading a dev team—until then, I'll settle for debugging everything. 🔍 🐞",
    " Tech enthusiast, stock market watcher, and part-time movie critic. 🎬 💻 📈",
    " I'm always up for a challenge, so let's build something awesome together! 🛠️ 🚀",
    " I'm currently looking for new opportunities, so feel free to reach out! 📧 🤝",
    " Thanks for visiting my portfolio! ☄️ 🚀",
]);

const renderedSegments = ref<TextSegment[]>([]);
const typingSpeed = 50;
const deleteSpeed = 40;
const pauseBetweenSentences = 3500; // 1.5 seconds pause between sentences
const showText = ref(false);
const isTyping = ref(false);

/**
 * Detects if a character is part of an emoji sequence and returns its full length.
 * Works for both emoji detection at start position and within an emoji.
 * ATTENTION: emojis needs to be divided by space or other characters to be detected correctly, otherwise it will be considered as a single emoji.
 */
const extractEmoji = (text: string, index: number, isDeleting: boolean = false): [boolean, number] => {
  // Check if the character at index is the start of an emoji
  let match = text.slice(index).match(/^(\p{Extended_Pictographic}|\p{Emoji_Presentation}|\p{Emoji}\uFE0F)(\u200D(\p{Extended_Pictographic}|\p{Emoji}))*/u);
  
  if (match) {
    return [true, match[0].length];
  }
  
  // Check if the character is inside an emoji sequence by scanning backwards working for deletion as well
  if (isDeleting) {
    for (let i = index; i >= 0; i--) {
      match = text.slice(i).match(/^(\p{Extended_Pictographic}|\p{Emoji_Presentation}|\p{Emoji}\uFE0F)(\u200D(\p{Extended_Pictographic}|\p{Emoji}))*/u);
      if (match && i + match[0].length > index) {
        // Character is within an emoji that started at position i
        return [true, i + match[0].length - index];
      }
    }
  }

  return [false, 1]; // Not part of any emoji
};


const typeText = async () => {
    showText.value = true;
    isTyping.value = true;

    // Initialize permanent segments
    renderedSegments.value = permanentSegments.value.map(segment => ({
        ...segment,
        text: ""
    }));

    // Type out permanent segments first
    for (let segmentIndex = 0; segmentIndex < permanentSegments.value.length; segmentIndex++) {
        const fullText = permanentSegments.value[segmentIndex].text;
        for (let charIndex = 0; charIndex < fullText.length; charIndex++) {
            await new Promise(resolve => setTimeout(resolve, typingSpeed));
            renderedSegments.value[segmentIndex].text += fullText[charIndex];
        }
    }

    // Add a dynamic segment for changing sentences
    renderedSegments.value.push({ text: "" });
    const dynamicIndex = renderedSegments.value.length - 1;

    while (true) {
        for (let sentenceIndex = 0; sentenceIndex < changingSegments.value.length; sentenceIndex++) {
            const sentence = changingSegments.value[sentenceIndex];

            renderedSegments.value[dynamicIndex].text = "";

            let charIndex = 0;
            while (charIndex < sentence.length) {
                const [isEmojiChar, emojiLength] = extractEmoji(sentence, charIndex);

                if (isEmojiChar) {
                    // **Type full emoji instantly**
                    await new Promise(resolve => setTimeout(resolve, typingSpeed));
                    renderedSegments.value[dynamicIndex].text += sentence.slice(charIndex, charIndex + emojiLength);
                    charIndex += emojiLength;
                } else {
                    // **Type normal characters one by one**
                    await new Promise(resolve => setTimeout(resolve, typingSpeed));
                    renderedSegments.value[dynamicIndex].text += sentence[charIndex];
                    charIndex++;
                }
            }

            // Wait before deleting
            await new Promise(resolve => setTimeout(resolve, pauseBetweenSentences));

            // Delete the sentence (emoji as whole units)
            while (renderedSegments.value[dynamicIndex].text.length > 0) {
                const currentText = renderedSegments.value[dynamicIndex].text;
                const lastCharPos = currentText.length - 1;

                // Find full emoji length before deletion
                const [isEmojiChar, emojiLength] = extractEmoji(currentText, lastCharPos, true);

                if (isEmojiChar) {
                    // **Delete full emoji at once**
                    await new Promise(resolve => setTimeout(resolve, deleteSpeed));
                    renderedSegments.value[dynamicIndex].text = currentText.slice(0,lastCharPos - emojiLength);
                } else {
                    // **Delete single character normally**
                    await new Promise(resolve => setTimeout(resolve, deleteSpeed));
                    renderedSegments.value[dynamicIndex].text = currentText.slice(0, -1);
                }
            }

            // Short pause before next sentence
            await new Promise(resolve => setTimeout(resolve, 500));
        }
    }
};



onMounted(() => {
    setTimeout(typeText, 500); // Delay before typing starts
});
</script>

<style scoped>
/* TODO better text va sopra lo schermo, metterlo dinamico, controllare il mobile */
.text-container {
  width: 75dvw;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
}

/* 📝 Text animation */
h1 {
  font-size: 1.8rem;
  line-height: 1.5;
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

@media screen and (max-width: 768px) {
  h1 {
    font-size: 1.75rem;
    line-height: 1.3;
  }
  .text-container {
    height: 0;
    margin: 50px 0 0 0;
  }
  
}

.highlight-name {
  font-weight: 800;
  font-size: 1.2em;
  color: #42b883; /* Vue green color, you can change this */
  text-shadow: 0 0 8px rgba(66, 184, 131, 0.5);
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

.cursor {
  display: inline-block;
  animation: blink 0.7s infinite;
}

@keyframes blink {
  50% {
    opacity: 0;
  }
}
</style>