<template>
  <v-app>
    <v-content>
      <Example/>
    </v-content>
  </v-app>
</template>

<script lang="ts">
import Vue from 'vue';
import Example from './components/Example.vue';

export default Vue.extend({
  name: 'App',

  components: {
    Example,
  },

  data: () => ({
    //
  }),
});
</script>
