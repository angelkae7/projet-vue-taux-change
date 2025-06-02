<template>
  <div class="home">
    <!-- le composant CompositionAPI est importé et utilisé ici -->
    <CompositionAPI />
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import CompositionAPI from '@/components/CompositionAPI.vue'

export default {
  name: 'HomeView',
  components: {
    HelloWorld,
    CompositionAPI
  }
}
</script>
