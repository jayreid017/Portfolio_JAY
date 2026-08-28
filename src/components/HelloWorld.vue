<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import gsap from 'gsap'
import { CircleChevronDown  } from 'lucide-vue-next'

const container = ref<HTMLElement | null>(null)
const name = ref<HTMLElement | null>(null)
const role = ref<HTMLElement | null>(null)

const mouseX = ref(0)
const mouseY = ref(0)
const isHovering = ref(false)

const updatePosition = (clientX: number, clientY: number) => {
  if (!container.value) return
  const rect = container.value.getBoundingClientRect()
  mouseX.value = clientX - rect.left
  mouseY.value = clientY - rect.top
}

// --- Character-by-character split (grapheme-aware) ---
// Uses Intl.Segmenter so combining marks (e.g. the Baybayin virama ᜔
// and vowel signs) stay attached to their base consonant instead of
// splitting into separate "glyphs".
function splitGraphemes(text: string): string[] {
  if (typeof Intl !== 'undefined' && 'Segmenter' in Intl) {
    const seg = new Intl.Segmenter('en', { granularity: 'grapheme' })
    return Array.from(seg.segment(text), (s) => s.segment)
  }
  return Array.from(text)
}

const FULL_NAME = 'Jay Bacurin'
const BAYBAYIN_NAME = 'ᜇᜒᜊᜌ᜔ ᜊᜃᜓᜇᜒᜈ᜔'

const nameChars = computed(() =>
  splitGraphemes(FULL_NAME).map((ch) => (ch === ' ' ? '\u00A0' : ch))
)
const baybayinChars = computed(() =>
  splitGraphemes(BAYBAYIN_NAME).map((ch) => (ch === ' ' ? '\u00A0' : ch))
)

const STEP_MS = 20
const SUB_STEP_MS = 15

onMounted(() => {
  const tl = gsap.timeline()

  tl.from(name.value, {
    y: 80,
    opacity: 0,
    duration: 1,
    ease: 'power3.out'
  }).from(
    role.value,
    {
      y: 80,
      opacity: 0,
      duration: 1,
      ease: 'power3.out'
    },
    '-=0.8'
  )
})

// Mouse Handlers (Desktop)
const handleMouseMove = (event: MouseEvent) => {
  updatePosition(event.clientX, event.clientY)
}

const handleMouseEnter = (event: MouseEvent) => {
  isHovering.value = true
  updatePosition(event.clientX, event.clientY)
}

const handleMouseLeave = () => {
  isHovering.value = false
}

// Touch Handlers (Mobile / Tablets)
const handleTouchStart = (event: TouchEvent) => {
  if (event.touches.length > 0) {
    isHovering.value = true
    const touch = event.touches[0]
    if (touch) updatePosition(touch.clientX, touch.clientY)
  }
}

const handleTouchMove = (event: TouchEvent) => {
  if (event.touches.length > 0) {
    const touch = event.touches[0]
    if (touch) updatePosition(touch.clientX, touch.clientY)
  }
}

const handleTouchEnd = () => {
  isHovering.value = false
}
</script>

<template>
    <section>
  <section class="md:flex justify-between items-center mt-20 lg:mt-10 mb-6 m-auto">
    <div
      ref="container"
      class="relative md:w-125 md:h-150 w-80 h-96 overflow-hidden rounded-2xl cursor-none m-6 touch-none select-none"
      @mousemove="handleMouseMove"
      @mouseenter="handleMouseEnter"
      @mouseleave="handleMouseLeave"
      @touchstart.passive="handleTouchStart"
      @touchmove.passive="handleTouchMove"
      @touchend="handleTouchEnd"
      @touchcancel="handleTouchEnd"
    >
      <!-- IMAGE 1 : BASE -->
      <img
        src="/src/assets/img/Front_Profile_Image.png"
        class="absolute inset-0 w-full h-full object-cover object-center pointer-events-none"
        alt="Base image"
      />

      <!-- IMAGE 2 : REVEAL -->
      <div
        v-if="isHovering"
        class="absolute inset-0 pointer-events-none"
        :style="{
          clipPath: `circle(100px at ${mouseX}px ${mouseY}px)`
        }"
      >
        <img
          src="/src/assets/img/Back_Profile_Image.png"
          class="absolute inset-0 w-full h-full object-cover object-center translate-x-[-1%] translate-y-1.5 md:translate-y-2 pointer-events-none"
          alt="Reveal image"
        />
      </div>
    </div>

    <div class="flex justify-center items-center mt-10">
      <div class="flex flex-col justify-center items-center">
        <div ref="name" class="group mb-2 relative flex flex-col justify-center items-center">
          <!-- English, split char by char -->
          <p class="font-montserrat text-4xl uppercase font-bold tracking-tight flex">
            <span
              v-for="(ch, i) in nameChars"
              :key="'name-' + i"
              class="inline-block transition-all duration-500 ease-out group-hover:opacity-0 group-hover:-translate-y-3"
              :style="{ transitionDelay: `${i * STEP_MS}ms` }"
            >{{ ch }}</span>
          </p>

          <!-- Baybayin replaces English, split char by char -->
          <p class="absolute font-baybayin text-4xl text-[#fbad8c] font-bold mb-6 flex">
            <span
              v-for="(ch, i) in baybayinChars"
              :key="'main-' + i"
              class="inline-block opacity-0 translate-y-3 transition-all duration-500 ease-out group-hover:opacity-100 group-hover:translate-y-0"
              :style="{ transitionDelay: `${i * STEP_MS}ms` }"
            >{{ ch }}</span>
          </p>

          <!-- Permanent Baybayin underneath, split char by char -->
          <p class="text-sm text-center font-baybayin font-bold flex justify-center">
            <span
              v-for="(ch, i) in baybayinChars"
              :key="'sub-' + i"
              class="inline-block transition-opacity duration-500 ease-out group-hover:opacity-40"
              :style="{ transitionDelay: `${i * SUB_STEP_MS}ms` }"
            >{{ ch }}</span>
          </p>
        </div>

        <p ref="role" class="text-sm font-montserrat text-gray-400">
          Web Developer
        </p>
      </div>
    </div>
  </section>
     <section
      class=" hidden absolute bottom-8 left-1/2 -translate-x-1/2
              md:flex gap-2 justify-center items-center"
      >
      <p class="font-montserrat text-xs uppercase text-gray-400 tracking-tight">
        Explore
      </p>

      <CircleChevronDown
        class="w-3 h-3 text-gray-400"
      />
    </section>
</section>
</template>