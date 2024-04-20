<template>
  <div class="container">
    <div class="santa santa_left"></div>
    <div class="presents">
      <IconNPELogo v-for="(index) in 3" :key="index" />
    </div>
    <div class="santa santa_right"></div>
  </div>
</template>

<script setup>
  import IconNPELogo from '@components/icons/IconNPELogo.vue'
</script>

<style scoped>
.container {
  display: flex;
  align-items: center;
  justify-content: center;
}

.santa {
  position: relative;
  width: 300px;
  height: 300px;
  background-size: cover;
  animation-duration: 1s;
  animation-iteration-count: infinite;
  animation-timing-function: linear;
  animation-direction: alternate;
}

.santa_left {
  background-image: url('../assets/santa_left.png');
  animation-name: santaDance_left;
}

.santa_right {
  background-image: url('../assets/santa_right.png');
  animation-name: santaDance_right;
}

@keyframes santaDance_left   {
    0% { transform: translateX(0); }
    100% { transform: translateX(-40px) rotate(-10deg); }
  }

@keyframes santaDance_right   {
    0% { transform: translateX(0); }
    100% { transform: translateX(40px) rotate(10deg); }
  }

.presents {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 3em;
  margin: 2em 0;
}
</style>
