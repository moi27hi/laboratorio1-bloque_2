<script setup>
import UnitConverter from './components/UnitConverter.vue'
</script>

<template>
  <div class="app-wrapper">
    <UnitConverter />
  </div>
</template>

<style>
* { margin: 0; padding: 0; box-sizing: border-box; }

body {
  background: #0e0e12;
  min-height: 100vh;
}

.app-wrapper {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>

