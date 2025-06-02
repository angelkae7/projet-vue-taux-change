<template>
  <div class="home">
    <!-- le composant TableauDevises est importé et utilisé ici -->
    <TableauDevises />
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import TableauDevises from '@/components/TableauDevises.vue'

export default {
  name: 'HomeView',
  components: {
    HelloWorld,
    TableauDevises
  }
}
</script>
