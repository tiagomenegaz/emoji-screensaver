<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';

const screenRef = ref<HTMLDivElement | null>(null);
const emojiRefs = ref<HTMLDivElement[]>([]);
const emojis = ref<{ text: string; x: number; y: number; speed: number; angle: number; dx: number; dy: number; style: Record<string, string> }[]>([]);
const allEmojis = ['😀', '😂', '😎', '🥳', '😍', '🚀', '✨', '💖', '🍕', '🎵'];
const numEmojis = 20;

let screenRect: DOMRect;
const padding = 10;
let animationFrame: number;

  const createEmoji = () => {
      for(let i = 0; i < numEmojis; i++){
        const emoji = {
          text: allEmojis[Math.floor(Math.random() * allEmojis.length)],
          x: Math.random() * (screenRect.width - padding * 2) + padding,
          y: Math.random() * (screenRect.height - padding * 2) + padding,
          speed: 2 + Math.random() * 3,
          angle: Math.random() * 2 * Math.PI,
          dx: 0,
          dy:0,
          style: {} as Record<string, string>
        };
          emoji.dx = Math.cos(emoji.angle) * emoji.speed;
          emoji.dy = Math.sin(emoji.angle) * emoji.speed;
          emoji.style.left = `${emoji.x}px`
          emoji.style.top = `${emoji.y}px`
          emojis.value.push(emoji);
      }
};


const animate = () => {
    for(let i = 0; i < emojis.value.length; i++){
        const emoji = emojis.value[i];
        const emojiRect = emojiRefs.value[i]?.getBoundingClientRect() || {width:0, height:0};

        emoji.x += emoji.dx;
        emoji.y += emoji.dy;
        if (emoji.x + emojiRect.width > screenRect.right - padding - screenRect.left|| emoji.x < screenRect.left + padding) {
              emoji.dx *= -1;
        }
          if (emoji.y + emojiRect.height > screenRect.bottom - padding - screenRect.top || emoji.y < screenRect.top + padding) {
            emoji.dy *= -1;
        }

        emoji.style.left = `${emoji.x}px`
        emoji.style.top = `${emoji.y}px`
      }

    animationFrame = requestAnimationFrame(animate);
  }

onMounted(() => {
    if(screenRef.value){
        screenRect = screenRef.value.getBoundingClientRect()
        createEmoji()
        animate()
    }
})

onUnmounted(() => {
    cancelAnimationFrame(animationFrame);
})
</script>

<template>
  <header>
    
  </header>

  <main>
    <div id="screen" ref="screenRef">
      <div
        v-for="(emoji, index) in emojis"
        :key="index"
        class="emoji"
        :style="emoji.style"
        ref="emojiRefs"
        >{{ emoji.text }}</div>
    </div>
  </main>
</template>

