<script setup lang="ts">
import { onMounted, onBeforeUnmount, ref } from 'vue'
import gsap from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

const sectionRef = ref<HTMLElement | null>(null)
const textEnRef = ref<HTMLElement | null>(null)
const textBaybayinRef = ref<HTMLElement | null>(null)
const texttagalogEnRef = ref<HTMLElement | null>(null)
const imageRef = ref<HTMLElement | null>(null)

let scrollTriggerInstance: ScrollTrigger | undefined
let timeline: gsap.core.Timeline | undefined

onMounted(() => {
  timeline = gsap.timeline({
    defaults: { ease: 'power3.out' },
    scrollTrigger: {
      trigger: sectionRef.value,
      start: 'top 80%', // animation starts once the section's top hits 80% down the viewport
      toggleActions: 'play none none reverse', // plays on enter, reverses if scrolled back up past it
      // markers: true, // uncomment while tuning the trigger point, remove before shipping
    },
  })

  scrollTriggerInstance = timeline.scrollTrigger

  timeline
    .set([textEnRef.value, textBaybayinRef.value, texttagalogEnRef.value], { opacity: 0, y: 40 })
    .set(imageRef.value, { opacity: 0, y: 60, scale: 1.04 })
    .to(textEnRef.value, { opacity: 1, y: 0, duration: 3 })
    .to(
      texttagalogEnRef.value,
      { opacity: 1, y: 0, duration: 3 },
      '<0.15'
    )
    .to(
      textBaybayinRef.value,
      { opacity: 1, y: 0, duration: 3 },
      '<0.70'
    )
    .to(
      imageRef.value,
      { opacity: 1, y: 0, scale: 1, duration: 1.1 },
      '<0.1'
    )
})

onBeforeUnmount(() => {
  scrollTriggerInstance?.kill()
  timeline?.kill()
})
</script>

<template>
  <section
    ref="sectionRef"
    class="relative w-full overflow-hidden h-[clamp(16rem,45vw,51.5rem)]"
  >
    <!-- BACKGROUND TEXT -->
    <div
      class="absolute inset-0 flex flex-col items-center justify-start
             pointer-events-none select-none z-0"
    >
      <h1
        ref="textEnRef"
        class="font-montserrat font-black uppercase
               text-[clamp(2.75rem,14vw,12rem)]
               leading-none whitespace-nowrap
               tracking-[-0.08em]
               -skew-y-3
               translate-y-3"
      >
        Projects
      </h1>
       <h1
        ref="texttagalogEnRef"
        class="font-montserrat font-black text-gray-300 uppercase
               text-[clamp(2.70rem,13.5vw,12rem)]
               leading-none whitespace-nowrap
               tracking-[-0.08em]
               -skew-y-3
               translate-y-3"
      >
        Proyekto
      </h1>

      <h2
        ref="textBaybayinRef"
        class="text-[#f8c6b1] font-baybayin uppercase
               text-[clamp(2.25rem,14vw,12rem)]
               leading-none whitespace-nowrap
               tracking-[-0.02em]
               -skew-y-3
               translate-y-3"
      >
        ᜉ᜔ᜇᜓᜐᜒᜃ᜔ᜆᜓᜐ᜔
      </h2>
    </div>

    <!-- IMAGE IN FRONT -->
    <div
      class="absolute inset-0 flex justify-center items-end z-10"
    >
      <img
        ref="imageRef"
        src="../assets/img/projects/FullSize.png"
        alt="Projects"
        class="h-full w-[clamp(6rem,20vw,15rem)] object-cover"
      />
    </div>
  </section>
</template>