<script setup lang="ts">
import { onMounted, onUnmounted } from 'vue'
import gsap from 'gsap'
import ScrollTrigger from 'gsap/ScrollTrigger'

import HelloWorld from './components/HelloWorld.vue'
import navbar from './components/navbar.vue'
import Projects from './components/Projects.vue'

gsap.registerPlugin(ScrollTrigger)

let ctx: gsap.Context

onMounted(() => {
  ctx = gsap.context(() => {
    const tl = gsap.timeline({
      scrollTrigger: {
        trigger: '.page-container',
        start: 'top top',
        end: '+=100%',
        scrub: true,
        pin: true,
      }
    })

    // Homepage slightly scales down
    tl.to('.home-page', {
      scale: 0.94,
      ease: 'none'
    }, 0)

    // Second page comes from the bottom
    tl.fromTo(
      '.second-page',
      {
        yPercent: 100
      },
      {
        yPercent: 0,
        ease: 'none'
      },
      0
    )
  })
})

onUnmounted(() => {
  ctx?.revert()
})
</script>

<template>
  <main class="relative min-h-screen">
    <!-- NAVBAR (Fixed at top, unaffected by scroll pinning or page transitions) -->
    <header class="fixed top-0 left-0 right-0 z-50 pointer-events-none">
      <div class="pointer-events-auto">
        <navbar />
      </div>
    </header>

    <!-- SCROLL CONTAINER -->
    <div class="page-container relative overflow-hidden">
      <!-- HOMEPAGE -->
      <section
        class="home-page relative z-10 flex min-h-screen items-center justify-center"
      >
        <HelloWorld />
      </section>

      <!-- SECOND PAGE -->
      <section
        class="second-page absolute inset-0 z-20  min-h-screen  bg-[#f6f6f6] border border-gray-200"
      >
      <Projects />
      </section>
    </div>
  </main>
</template>
