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
    " Back-end and Software Developer by day ☀️👨‍💻, problem-solving ninja by night. 🌙🥷",
    " I write code that works on the first try... that's what I like to think at least! 😅",
    " Turning swearings into APIs and database structures. 🤬➡️💻",
    " Fluent in PHP, JavaScript/TypeScript, Python, various Frameworks and fixing last-minute bugs. 🐛🔧",
    " I break problems, not production servers (well, almost never). 🤞💪",
    " Git guru, networking nerd, and database whisperer. 🧙‍♂️🔌",
    " Team player who actually enjoys code reviews (weird, right?). 🤓👍",
    " Passionate about coding, teaching, and finding the perfect GIF for every occasion. 💻👨‍🏫🎭",
    " Dreaming of leading a dev team—until then, I'll settle for debugging everything. 🔍🐞",
    " Tech enthusiast, stock market watcher, and part-time movie critic. 🎬💻📈",
    " I'm always up for a challenge, so let's build something awesome together! 🛠️🚀",
    " I'm currently looking for new opportunities, so feel free to reach out! 📧🤝",
    " Thanks for visiting my portfolio! ☄️🚀",
]);

const renderedSegments = ref<TextSegment[]>([]);
const typingSpeed = 50;
const deleteSpeed = 30;
const pauseBetweenSentences = 3500; // 1.5 seconds pause between sentences
const showText = ref(false);
const isTyping = ref(false);

// Simple regex to detect emoji sequences
const emojiRegex = /(\p{Emoji_Presentation}|\p{Emoji}\uFE0F)/u;

// Function to check if a character at a position is part of an emoji
const isEmoji = (text: string, index: number): [boolean, number] => {
    // Check if this could be the start of an emoji sequence
    if (emojiRegex.test(text.slice(index, index + 2))) {
        // Some emoji are 2 characters (basic emoji)
        if (emojiRegex.test(text.slice(index, index + 2)) && 
            !emojiRegex.test(text.slice(index, index + 3))) {
            return [true, 2];
        }
        // Some emoji are combinations with modifiers (skin tone, gender, etc.)
        else if (emojiRegex.test(text.slice(index, index + 4))) {
            return [true, 4];
        }
        // Complex emoji with ZWJ sequences (family, couple, etc.)
        else if (text.slice(index, index + 5).includes('\u200D')) {
            // Find the end of this emoji sequence
            let i = index + 5;
            while (i < text.length && (text[i] === '\u200D' || emojiRegex.test(text[i]))) {
                i++;
            }
            return [true, i - index];
        }
        // Basic single emoji
        return [true, 1];
    }
    return [false, 1];
};

const typeText = async () => {
    showText.value = true;
    isTyping.value = true;
    
    // Initialize with permanent segments first
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
    
    // Add a dynamic segment that will change
    renderedSegments.value.push({ text: "" });
    const dynamicIndex = renderedSegments.value.length - 1;
    
    // Cycle through all changing segments
    while (true) {
        for (let sentenceIndex = 0; sentenceIndex < changingSegments.value.length; sentenceIndex++) {
            const sentence = changingSegments.value[sentenceIndex];
            
            // Type the sentence character by character
            let charIndex = 0;
            while (charIndex < sentence.length) {
                // Check if this is an emoji
                const [isEmojiChar, emojiLength] = isEmoji(sentence, charIndex);
                
                if (isEmojiChar) {
                    await new Promise(resolve => setTimeout(resolve, typingSpeed * 2));
                    // Add the entire emoji sequence at once
                    renderedSegments.value[dynamicIndex].text += sentence.slice(charIndex, charIndex + emojiLength);
                    charIndex += emojiLength;
                } else {
                    await new Promise(resolve => setTimeout(resolve, typingSpeed));
                    renderedSegments.value[dynamicIndex].text += sentence[charIndex];
                    charIndex++;
                }
            }
            
            // Wait before deleting
            await new Promise(resolve => setTimeout(resolve, pauseBetweenSentences));
            
            // Delete the sentence (handle emojis during deletion too)
            while (renderedSegments.value[dynamicIndex].text.length > 0) {
                const currentText = renderedSegments.value[dynamicIndex].text;
                const lastCharPos = currentText.length - 1;
                
                // Check if the last character is part of an emoji
                const lastChar = currentText[lastCharPos];
                const prevChar = lastCharPos > 0 ? currentText[lastCharPos - 1] : '';
                
                // Delete either one character or the entire emoji
                if (emojiRegex.test(lastChar) || 
                    emojiRegex.test(prevChar + lastChar) || 
                    (lastCharPos > 1 && emojiRegex.test(currentText.slice(lastCharPos - 2, lastCharPos + 1)))) {
                    // Find how many characters to delete for this emoji
                    let charsToDelete = 1;
                    if (lastCharPos > 0 && emojiRegex.test(prevChar + lastChar)) {
                        charsToDelete = 2;
                    } else if (lastCharPos > 1 && 
                               currentText.slice(lastCharPos - 2, lastCharPos).includes('\u200D')) {
                        // Complex emoji with ZWJ sequence
                        charsToDelete = 2;
                        while (lastCharPos - charsToDelete >= 0 && 
                              (currentText[lastCharPos - charsToDelete] === '\u200D' || 
                               emojiRegex.test(currentText[lastCharPos - charsToDelete]))) {
                            charsToDelete++;
                        }
                    }
                    
                    await new Promise(resolve => setTimeout(resolve, deleteSpeed * 2));
                    renderedSegments.value[dynamicIndex].text = 
                        renderedSegments.value[dynamicIndex].text.slice(0, -charsToDelete);
                } else {
                    await new Promise(resolve => setTimeout(resolve, deleteSpeed));
                    renderedSegments.value[dynamicIndex].text = 
                        renderedSegments.value[dynamicIndex].text.slice(0, -1);
                }
            }
            
            // Short pause before typing next sentence
            await new Promise(resolve => setTimeout(resolve, 500));
        }
    }
};

onMounted(() => {
    setTimeout(typeText, 500); // Delay before typing starts
});
</script>

<style scoped>
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