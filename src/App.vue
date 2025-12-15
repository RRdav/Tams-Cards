<script setup lang="ts">
import MTG from './components/MTG.vue';
</script>

<template>
  <header>
    Velkamment til min Vue 3 app!
  </header>

  <main>
    <MTG />
  </main>
</template>

<style scoped>

  main {
    justify-content: center;
    display: flex;
    height: 100%;
  }

</style>
