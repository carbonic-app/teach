<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
          transition="scale-transition"
          width="40"
        />

        <v-img
          alt="Vuetify Name"
          class="shrink mt-1 hidden-sm-and-down"
          contain
          min-width="100"
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-name-dark.png"
          width="100"
        />
      </div>

      <v-spacer></v-spacer>

      <v-btn
        @click="createUser"
        text
      >
        <span class="mr-2">Create Account</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>

    <vuetify-toast/>

  </v-app>
</template>

<script>
import { AccountServiceClient, CreateRequest } from "./account_service_grpc_web_pb";
import VuetifyToast from './components/toast'; // https://github.com/eolant/vuetify-toast-snackbar

export default {
  name: 'App',

  components: {
    VuetifyToast
  },

  data: () => ({
    users: []
  }),

  created: function() {
    this.$vuetify.theme.dark = true;
    this.client = new AccountServiceClient("http://34.82.179.230", null, null);
    // eslint-disable-next-line no-console
    console.log(this.client);
    // this.getUsers();
  },

  methods: {
    // getUsers: function() {
    //   // NOT IMPLEMENTED IN BACKEND YET

    //   // Set up stream
    //   let req = new GetUsersRequest();
    //   this.client.getUsers(req, {}, (err, res) => {
    //     this.users = res.toObject().userList;
    //     console.log(this.users);
    //   })

    //   // Receive data
    //   let stream = this.client.getUsers(req, {}, {});
    //   stream.on('data', (res) => {
    //     this.users.push(res.getUser());
    //   });
    // },

    createUser: function() {
      let req = new CreateRequest;
      // Hardcoded for testing
      let username = 'gg';
      let pw = 'pw';
      req.setUsername(username);
      req.setPassword(pw);
      this.client.create(req, {}, (err, res) => {
        if (!err) {
          this.$toast.success('Created user. Token: ' + res, {queueable: true});
        } else {
          this.$toast.error('Error code ' + err.code + ' when creating user: ' + err.message);
          // eslint-disable-next-line no-console
          console.log(err);
        }
      });
    }
  }
};
</script>
