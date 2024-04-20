
<script setup>
import { ref, computed, watch, onMounted, onUnmounted } from 'vue'

import { RouterView } from 'vue-router'

import IconNPELogo from '@components/icons/IconNPELogo.vue'
import HelloWorld from '@components/HelloWorld.vue'
import Banner from '@components/Banner.vue'
import Footer from '@components/Footer.vue'


const scrollTop = ref(window.scrollY)

// if distance < rayon de 80px (par exemple)
// on bouge les snowflakes
// sinon on n'y touche pas
// l'effet diminue plus on est loin
// du genre : 79px : facteur 0.1, 1px : 0.9
// trop complexe maintenant, on essaiera plus tard


</script>

<template>
  <header>
    <Banner />
  </header>

  <main>
    <div class="snowflakes">
      <div
      v-for="(i) in 100"
      class="snowflake"
      :style="{
        left: `${Math.random() * 100}vw`,
        top: `${Math.random() * scrollTop}px`,
        animationDelay: `${0.1 * i * Math.random()}s`,
        animationDuration: `${Math.random() * 6 + 4}s`,
      }">
      </div>
    </div>

    <div style="display: flex; justify-content: space-around; align-items: center; margin-bottom: -2em;">
      <IconNPELogo style="scale: 0.5; justify-self: end;"/>
      <HelloWorld msg="Welcome on the NorthPoleExchange" />
      <IconNPELogo style="scale: 0.5;"/>
    </div>
    <RouterView />
  </main>

  <footer>
    <Footer />
  </footer>
</template>

<style scoped>
.snowflakes {
  position: fixed;
  top: 0;
  z-index: -1;
}

.snowflake {
  z-index: -1;
  position: absolute;
  background: #fff;
  border-radius: 50%;
  pointer-events: none;
  box-shadow: 0 0 10px #fff;
  animation: snowfall linear infinite;
  width: 5px;
  height: 5px;
}

@keyframes snowfall {
  0% {
    transform: translateY(-5vh);
  }
  100% {
    transform: translateY(99vh);
  }
}

header {
  line-height: 1.5;
  max-height: 100vh;
  display: flex;
  flex-direction: column;
}

footer {
  margin: 0;
}

nav {
  width: 100%;
  font-size: 12px;
  text-align: center;
  margin-top: 2rem;
}

nav a.router-link-exact-active {
  color: var(--color-text);
}

nav a.router-link-exact-active:hover {
  background-color: transparent;
}

nav a {
  display: inline-block;
  padding: 0 1rem;
  border-left: 1px solid var(--color-border);
}

nav a:first-of-type {
  border: 0;
}

/* @media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  nav {
    text-align: left;
    margin-left: -1rem;
    font-size: 1rem;

    padding: 1rem 0;
    margin-top: 1rem;
  }
} */
</style>