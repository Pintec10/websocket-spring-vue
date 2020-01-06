<template>
  <v-container class="my-5">
    <v-row class="text-center">
      <span class="mx-3">Connection controls:</span>
      <v-btn class="mx-1" @click.prevent="connect" :disabled="connected">Connect</v-btn>
      <v-btn class="mx-1" @click.prevent="disconnect" :disabled="!connected">Disconnect</v-btn>
    </v-row>

    <v-row>
      <v-subheader class="text-right">Please enter a name:</v-subheader>
      <v-text-field label="name" v-model="enteredText"></v-text-field>
      <v-btn @click.prevent="sendMessage" :disabled="!connected">
        <v-icon class="mr-1">mdi-send</v-icon>SEND
      </v-btn>
    </v-row>

    <div>
      <v-simple-table v-slot:default>
        <thead>
          <tr>
            <th class="text-bold">Message</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in messages" :key="item.message">
            <td>{{item.message}}</td>
          </tr>
        </tbody>
      </v-simple-table>
    </div>
  </v-container>
</template>

<script>
import SockJS from "sockjs-client";
import Stomp from "webstomp-client";

export default {
  name: "WebsocketGreetings",

  data() {
    return {
      connected: false,
      enteredText: "",
      messages: []
    };
  },

  methods: {
    sendMessage() {
      console.log("Text entered:" + this.enteredText);
      if (this.stompClient && this.stompClient.connected) {
        let msg = { name: this.enteredText };
        console.log("Sending:");
        console.log(JSON.stringify(msg));
        this.stompClient.send("/app/hello", JSON.stringify(msg), {});
      }
    },

    connect() {
      this.socket = new SockJS("http://localhost:8080/gs-guide-websocket");
      this.stompClient = Stomp.over(this.socket);
      this.stompClient.connect(
        {},
        frame => {
          this.connected = true;
          console.log("Frame:");
          console.log(frame);
          this.stompClient.subscribe("/topic/greetings", tick => {
            console.log("tick received:");
            console.log(tick);
            let newMessage = { message: JSON.parse(tick.body).content };
            this.messages.push(newMessage);
            console.log("Now the messages array is:");
            console.log(this.messages);
            this.enteredText = "";
          });
        },
        error => {
          console.log(error);
          this.connected = false;
        }
      );
    },

    disconnect() {
      if (this.stompClient) {
        this.stompClient.disconnect();
      }
      this.connected = false;
    }
  }
};
</script>

<style>
</style>