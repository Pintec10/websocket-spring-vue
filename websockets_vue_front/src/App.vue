<template>
  <v-app>
    <v-app-bar app color="green" dark>
      <div class="d-flex align-center">
        <h1>Websockets</h1>
      </div>

      <v-spacer></v-spacer>
    </v-app-bar>

    <v-content>
      <WebsocketGreetings />
    </v-content>
  </v-app>
</template>

<script>
import WebsocketGreetings from "./components/WebsocketGreetings";

export default {
  name: "App",

  components: {
    WebsocketGreetings
  },

  data: () => ({
    //
  })
};
</script>
