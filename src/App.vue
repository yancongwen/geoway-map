<template>
  <div id="app">
    <v-header></v-header>
  </div>
</template>

<script>
import Header from '@/components/Header.vue'

export default {
  name: 'App',
  components: {
    'v-header': Header
  }
}
</script>

<style>
#app {
}
</style>
