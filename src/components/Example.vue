<template>
  <v-container>
    <conditionbuilder :parentobject="expression" fieldname="value" label="Condition" rows="2" />
  </v-container>
</template>

<script lang="ts">
  import Vue from 'vue'
  
import conditionbuilder from './conditionbuilder/index.vue';

  export default Vue.extend({
    name: 'Example',
    components:{
      conditionbuilder
    },
    data: () => ({
      expression:{
        value:''
      }
    }),
  })
</script>
