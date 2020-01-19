<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <v-toolbar-title>Rock Scissors Paper Game</v-toolbar-title>
      </div>
      <v-spacer></v-spacer>
    </v-app-bar>

    <v-content>
      <Rsp/>
    </v-content>
  </v-app>
</template>

<script>
import Rsp from '@/components/Rsp';

export default {
  name: 'App',

  components: {
    Rsp,
  },

  data: () => ({
    //
  }),
};
</script>