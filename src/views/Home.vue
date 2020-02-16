<template>
  <v-container>
    <Example/>
    Hello
  </v-container>
</template>

<script lang="ts">
  import Vue from 'vue'
  import Example from '../components/Example.vue';

  export default Vue.extend({
    name: 'Home',

    data: () => ({

    }),
  })
</script>
