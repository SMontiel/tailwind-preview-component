<template>
  <TailwindPreviewComponent :host="host"/>
</template>

<script>
import TailwindPreviewComponent from './components/TailwindPreviewComponent.vue'

export default {
  name: 'app',
  components: {
    TailwindPreviewComponent
  },
  data() {
    return {
      host: window.location.href
    };
  }
}
</script>

<style scoped>
</style>
