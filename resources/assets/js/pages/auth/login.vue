<template>
  <v-layout row>
    <br />
    <v-flex xs12 sm8 offset-sm2 lg4 offset-lg4>
      <v-card>
        <progress-bar :show="busy"></progress-bar>
        <form @submit.prevent="login" @keydown="form.onKeydown($event)">
          <v-card-title primary-title>
            <h3 class="headline mb-0">{{ $t('login') }}</h3>
          </v-card-title>
          <v-card-text>
            <!-- Email -->
            <text-input
              :form="form"
              :label="$t('Username')"
              :v-errors="errors"
              :value.sync="form.username"
              name="Username"
              prepend="person_outline"
              v-validate="'required'"
            ></text-input>

            <!-- Password -->
            <password-input
              :v-errors="errors"
              :form="form"
              :value.sync="form.password"
              prepend="lock_outline"
              v-validate="'required'"
            ></password-input>

            <!-- Remember Me -->
            <v-checkbox
              :label="$t('remember_me')"
              color="primary"
              type="checkbox"
              v-model="remember"
              value="true"
            ></v-checkbox>

            <submit-button :block="true" :form="form" :label="$t('login')"></submit-button>
          </v-card-text>
        </form>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import Form from "vform";

export default {
  name: "login-view",
  metaInfo() {
    return { title: this.$t("login") };
  },
  data: () => ({
    form: new Form({
      username: "",
      password: ""
    }),
    eye: true,
    remember: false,
    busy: false
  }),
  created() {

  },
  methods: {
    async login() {
      if (await this.formHasErrors()) return;
      this.busy = true;

      // Submit the form.
      const { data } = await this.form.post("api/login");

      // Save the token.
      this.$store.dispatch("saveToken", {
        token: data.token,
        remember: this.remember
      });

      // Fetch the user.
      await this.$store.dispatch("fetchUser");
      this.busy = false;

      // Redirect home.
        this.$router.push({ name: "home" });
      if (this.$store.state.auth.user.level === "packing") {
        this.$router.push({ name: "packing" });
      }

      if (this.$store.getters.authUser.level == "ppic") {
        this.$router.push({ name: "monitoring" });
      }

       if (this.$store.getters.authUser.level == "leveldua") {
        this.$router.push({ name: "input_bongkaran" });
      }

      if (this.$store.getters.authUser.level == "gudang") {
        this.$router.push({ name: "transfer_bongkaran" });
      }





    }
  }
};
</script>
