<script setup>
import GroupInfoComponent from '../components/GroupInfoComponent.vue'
import GroupSecretComponent from '../components/GroupSecretComponent.vue'

</script>

<template>7
<div class="grid-wrapper">
  <GroupInfoComponent id="GroupInfoComponent"/>
  <GroupSecretComponent id="GroupSecretComponent"/>
</div>
</template>

<style scoped>
  .grid-wrapper {
    border-radius: 0.3em;
    gap: 0;
    padding: 0.25em 0.8em;
  }
  #GroupInfoComponent {
    justify-self: end;
  }

  #GroupSecretComponent {
    justify-self: start;
  }
    

</style>
