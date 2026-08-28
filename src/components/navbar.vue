<script setup lang="ts">
import { 
    Menu, 
    MessageCircleMore, 
    FolderOpenDot, 
    House, 
    UserRound, 
    X,
    Mail,
    Send,
    Phone,
    Linkedin
} from 'lucide-vue-next';
import gsap from 'gsap';
import { ref } from 'vue';

type NavState = 'closed' | 'menu' | 'message';

const navState = ref<NavState>('closed');

const onEnter = (el: Element, done: () => void) => {
    const isClosed = el.classList.contains('state-closed');
    const rollBtn = el.querySelector('.roll-item');
    const otherItems = el.querySelectorAll('.anim-item:not(.roll-item)');

    if (isClosed) {
        // Closed state buttons & logo glide into place smoothly
        const closedItems = el.querySelectorAll('.anim-item');
        gsap.fromTo(
            closedItems,
            {
                opacity: 0,
                x: 35,
                scale: 0.9,
            },
            {
                opacity: 1,
                x: 0,
                scale: 1,
                duration: 0.3,
                stagger: 0.04,
                ease: 'power3.out',
                clearProps: 'transform,scale',
                onComplete: done,
            }
        );
    } else {
        // 1. Rolling EXIT button (rolls & spins smoothly into its spot from the right)
        if (rollBtn) {
            gsap.fromTo(
                rollBtn,
                {
                    opacity: 0,
                    x: 90,
                    rotation: -360,
                    scale: 0.7,
                },
                {
                    opacity: 1,
                    x: 0,
                    rotation: 0,
                    scale: 1,
                    duration: 0.42,
                    ease: 'power3.out',
                    clearProps: 'transform,scale,rotate',
                }
            );
        }

        // 2. Navigation / Message items glide in smoothly from the right
        gsap.fromTo(
            otherItems,
            {
                opacity: 0,
                x: 60,
                scale: 0.9,
            },
            {
                opacity: 1,
                x: 0,
                scale: 1,
                duration: 0.38,
                stagger: 0.05,
                delay: 0.04,
                ease: 'power3.out',
                clearProps: 'transform,scale',
                onComplete: done,
            }
        );
    }
};

const onLeave = (el: Element, done: () => void) => {
    const isClosed = el.classList.contains('state-closed');
    const rollBtn = el.querySelector('.roll-item');
    const otherItems = el.querySelectorAll('.anim-item:not(.roll-item)');

    if (isClosed) {
        const closedItems = el.querySelectorAll('.anim-item');
        gsap.to(closedItems, {
            opacity: 0,
            x: 30,
            scale: 0.9,
            duration: 0.2,
            stagger: 0.03,
            ease: 'power2.in',
            onComplete: done,
        });
    } else {
        // Exit button rolls out to the right
        if (rollBtn) {
            gsap.to(rollBtn, {
                opacity: 0,
                x: 60,
                rotation: 360,
                scale: 0.7,
                duration: 0.25,
                ease: 'power2.in',
            });
        }

        // Other items slide out to the right in reverse
        gsap.to(otherItems, {
            opacity: 0,
            x: 50,
            scale: 0.9,
            duration: 0.22,
            stagger: {
                each: 0.03,
                from: 'end',
            },
            ease: 'power2.in',
            onComplete: done,
        });
    }
};
</script>

<template>
    <section class="flex justify-center items-center m-2 mt-10">
        <Transition 
            mode="out-in" 
            :css="false" 
            @enter="onEnter" 
            @leave="onLeave"
        >
            <!-- 1. CLOSED STATE (Logo + Menu + Message) -->
            <div 
                v-if="navState === 'closed'" 
                key="closed" 
                class="state-closed flex items-center gap-2"
            >
                <!-- Logo -->
                 <div
                    class="anim-item
                            bg-white shadow-sm rounded-2xl
                            flex justify-center items-center p-2
                            cursor-pointer
                            transition-all duration-400 ease-out
                            hover:scale-105
                            active:scale-95
                    "
                    >
                    <img
                        src="../assets/img/logo_TR1.png"
                        alt="logo"
                        class="w-10 h-10 transition-transform duration-400 ease-out"
                    />
                </div>

                <!-- Menu Button -->
                <button
                    @click="navState = 'menu'"
                    aria-label="Open menu"
                    class="anim-item bg-white shadow-sm rounded-2xl flex justify-center items-center p-5 cursor-pointer text-gray-700 hover:scale-105 hover:text-[#facabf] active:scale-95 transition-all duration-200"
                >
                    <Menu class="w-4 h-4" />
                </button>

                <!-- Message Button -->
                <button
                    @click="navState = 'message'"
                    aria-label="Open messages"
                    class="anim-item bg-white shadow-sm rounded-2xl flex justify-center items-center p-5 cursor-pointer text-gray-700 hover:scale-105 hover:text-[#facabf] active:scale-95 transition-all duration-200"
                >
                    <MessageCircleMore class="w-4 h-4"   />
                </button>
            </div>

            <!-- 2. MENU OPEN STATE -->
            <nav 
                v-else-if="navState === 'menu'" 
                key="menu" 
                class="state-menu flex items-center gap-2"
            >
                <!-- Rolling EXIT Button -->
                <button
                    @click="navState = 'closed'"
                    aria-label="Close menu"
                    class="anim-item roll-item shadow-sm rounded-full text-white bg-black  flex justify-center items-center p-5 cursor-pointer hover:scale-105 active:scale-95 transition-all duration-200"
                >
                    <X class="w-4 h-4" />
                </button>

                <!-- Menu Navigation Items -->
                <ul class="flex gap-2">
                    <li 
                        title="Home"
                        class="anim-item bg-white shadow-sm rounded-2xl flex justify-center items-center p-5 cursor-pointer hover:scale-105 text-gray-700 hover:text-[#facabf] active:scale-95 transition-all duration-200"
                    >
                        <House class="w-4 h-4 " />
                    </li>   
                    
                    <li 
                        title="Portfolio"
                        class="anim-item bg-white shadow-sm rounded-2xl flex justify-center items-center px-5 py-4 cursor-pointer hover:scale-105 text-gray-700 hover:text-[#facabf] active:scale-95 transition-all duration-200"
                    >
                        <FolderOpenDot class="w-4 h-4 " />
                    </li>

                    <li 
                        title="About / Profile"
                        class="anim-item bg-white shadow-sm rounded-2xl flex justify-center items-center px-5 py-4 cursor-pointer hover:scale-105 text-gray-700 hover:text-[#facabf] active:scale-95 transition-all duration-200"
                    >
                        <UserRound class="w-4 h-4 " />
                    </li>
                </ul>
            </nav>

            <!-- 3. MESSAGE OPEN STATE -->
            <nav 
                v-else-if="navState === 'message'" 
                key="message" 
                class="state-message flex items-center gap-2"
            >
                <!-- Rolling EXIT Button -->
                <button
                    @click="navState = 'closed'"
                    aria-label="Close messages"
                    class="anim-item roll-item shadow-sm rounded-full text-white bg-black flex justify-center items-center p-5 cursor-pointer hover:scale-105 active:scale-95 transition-all duration-200"
                >
                    <X class="w-4 h-4" />
                </button>

                <!-- Message / Contact Items -->
                <ul class="flex gap-2">
                    <li 
                        title="Email"
                        class="anim-item bg-white shadow-sm rounded-2xl flex justify-center items-center p-5 cursor-pointer hover:scale-105 text-gray-700 hover:text-[#facabf] active:scale-95 transition-all duration-200"
                    >
                        <Mail class="w-4 h-4 " />
                    </li>
                    
                    <li 
                        title="Send Message"
                        class="anim-item bg-white shadow-sm rounded-2xl flex justify-center items-center px-5 py-4 cursor-pointer hover:scale-105 text-gray-700 hover:text-[#facabf] active:scale-95 transition-all duration-200"
                    >
                        <Send class="w-4 h-4 " />
                    </li>

                    <li 
                        title="Call / Phone"
                        class="anim-item bg-white shadow-sm rounded-2xl flex justify-center items-center px-5 py-4 cursor-pointer hover:scale-105 text-gray-700 hover:text-[#facabf] active:scale-95 transition-all duration-200"
                    >
                        <Phone class="w-4 h-4 " />
                    </li>

                    <li 
                        title="LinkedIn"
                        class="anim-item bg-white shadow-sm rounded-2xl flex justify-center items-center px-5 py-4 cursor-pointer hover:scale-105 text-gray-700 hover:text-[#facabf] active:scale-95 transition-all duration-200"
                    >
                        <Linkedin class="w-4 h-4 " />
                    </li>
                </ul>
            </nav>
        </Transition>
    </section>
</template>