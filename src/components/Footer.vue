<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

const footerRoutes = ref(router.getRoutes().filter((route) => route.name === 'About'))

</script>

<template>
  <nav class="footer">
    <RouterLink class="banner" v-for="(route) in footerRoutes" :to="route.path">{{ route.name }}</RouterLink>
  </nav>
</template>

<style scoped>

nav.footer {
  display: flex;
  margin: 0;
  
  align-items: center;
  justify-content: flex-start;
  gap: 1rem;
  
  height: var(--footer-height);
  background-color: var(--vt-c-black);
}

a.banner {
  display: flex;
  align-items: center;
  height: calc(var(--footer-height)*0.7);
  border-radius: 0;
  padding-inline: 1em;
}

nav > a {
  color: white;
  font-size: 1rem;
  padding-inline: 0.8em;
  border-radius: 0.4em;
}

nav > a:first-child {
  margin-left: 0.6em;
}
</style>