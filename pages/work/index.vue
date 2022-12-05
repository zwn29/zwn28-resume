<template>
  <div class="bg-black h-screen px-6 py-12">
    <h2
      class="text-white font-exo text-5xl opacity-0 py-1"
      ref="title"
      style="transform: translateY(40px)"
    >
      MY WORK
    </h2>
    <div class="flex space-x-4">
      <div
        ref="projects"
        v-for="project in projects"
        class="w-full opacity-0"
        style="transform: translateX(40px)"
      >
        <nuxt-link to="/">
          <img
            :src="project.image.url"
            alt="iphone"
            class="object-cover h-[70vh]"
          />
          <p class="text-white font-space-mono text-sm">
            {{ project.title }}
          </p>
        </nuxt-link>
      </div>
    </div>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Exo+2:ital,wght@1,700&family=Space+Mono&display=swap');
.font-exo {
  font-family: 'Exo 2', sans-serif;
}

.font-space-mono {
  font-family: 'Space Mono', monospace;
}
</style>

<script>
import iphoneImg from '~/assets/iphone.jpg'
import laptopImg from '~/assets/laptop.jpg'
import personImg from '~/assets/person.jpg'
import gsap from 'gsap'
export default {
  data() {
    return {
      projects: [
        {
          image: {
            url: iphoneImg,
          },
          title: 'IPHONE',
        },
        {
          image: {
            url: laptopImg,
          },
          title: 'LAPTOP',
        },
        {
          image: {
            url: personImg,
          },
          title: 'PERSON',
        },
        {
          image: {
            url: iphoneImg,
          },
          title: 'IPHONE',
        },
        {
          image: {
            url: laptopImg,
          },
          title: 'LAPTOP',
        },
        {
          image: {
            url: personImg,
          },
          title: 'PERSON',
        },
      ],
    }
  },
  mounted() {
    gsap.to(this.$refs.title, {
      opacity: 1,
      duration: 1.5,
      y: 0,
      ease: 'expo',
    })
    gsap.to(this.$refs.projects, {
      opacity: 1,
      duration: 1.5,
      stagger: 0.1,
      x: 0,
      ease: 'expo',
      delay: 0.3,
    })
  },
}
</script>
