<template>
  <v-app>
    <demo/>
  </v-app>
</template>

<script>
// import HelloWorld from './components/HelloWorld';
import demo from './components/demo'
export default {
  name: 'App',

  components: {
    // HelloWorld,
    demo
  },

  data: () => ({
    //
  }),
};
</script>
