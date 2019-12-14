<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        Carbonic
      </div>

      <v-spacer></v-spacer>

      <v-tooltip left>
        <template v-slot:activator="{ on }">
          <v-btn icon large
            href="https://github.com/carbonic-app"
            target="_blank" v-on="on"
          >
            <v-icon>mdi-github-circle</v-icon>
          </v-btn>
        </template>
        <span>Source</span>
      </v-tooltip>
    </v-app-bar>


    <v-content>
      <v-container
        class="fill-height"
        fluid
      >
        <v-row
          align="center"
          justify="center"
        >
          <v-col
            cols="12"
            sm="8"
            md="4"
          >
            <v-card class="elevation-12">
              <v-toolbar
                color="primary"
                dark
                flat
              >
                <v-toolbar-title>Register for Carbonic</v-toolbar-title>
              </v-toolbar>
              <v-card-text>
                <v-form>
                  <v-text-field v-model="username"
                    label="Username"
                    name="username"
                    prepend-icon="person"
                    type="text"
                  />

                  <v-text-field
                    id="password"
                    label="Password"
                    name="password"
                    prepend-icon="lock"
                    type="password"
                  />
                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-spacer />
                <v-btn @click="createUser" color="primary">Create account</v-btn>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>
        <v-col class="text-center">
          <v-btn>List Users</v-btn>
        </v-col>
      </v-container>
    </v-content>

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
    users: [],
    username: '',
    password: ''
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
      req.setUsername(this.username);
      req.setPassword(this.password);
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
