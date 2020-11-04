<template>
  <v-app>
    <PipelineTable/>
  </v-app>
</template>

<script>
import PipelineTable from './components/PipelineTable.vue'
export default {
  name: 'App',
  components: {
    PipelineTable
  }
}
</script>
