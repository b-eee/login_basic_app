<template>
  <div>
    <Header :is-guest="true"></Header>
    <div class="content">
      <v-responsive max-width="600" class="mx-auto text-center mt-16">
        <v-row
          class="my-0"
          justify="center"
          align-content="center"
          style="height: 100%"
        >
          <p class="text-center font-weight-bold text-h4">
            Registertion password
          </p>
          <v-col v-if="!isSend" cols="12">
            <v-row justify="center">
              <v-col cols="6">
                <ValidationProvider
                  v-slot="{ errors }"
                  rules="min:8|required"
                  name="password"
                >
                  <v-text-field
                    v-model="password"
                    label="password"
                    type="password"
                    :error-messages="errors"
                  ></v-text-field>
                </ValidationProvider>
              </v-col>
            </v-row>
            <v-row justify="center">
              <v-col cols="6">
                <ValidationProvider
                  v-slot="{ errors }"
                  rules="min:8|required"
                  name="password"
                >
                  <v-text-field
                    v-model="confirmPassword"
                    label="confirm new password"
                    type="password"
                    :error-messages="errors"
                  ></v-text-field>
                </ValidationProvider>
              </v-col>
            </v-row>
            <div class="text-center">
              <div>
                <p class="red--text">{{ errorMsg }}</p>
                <v-btn
                  color="primary mt-3"
                  width="180"
                  height="44"
                  tile
                  @click="signup"
                  >Sign up</v-btn
                >
              </div>
              <div class="mt-4">
                <router-link to="/login" class="black--text">Login</router-link>
              </div>
            </div>
          </v-col>

          <v-col v-else class="text-center" cols="12">
            <img src="/checkmark.png" />
            <p class="mt-5">Your SignUp completed.</p>
            <p>Please login</p>
            <div class="mt-8">
              <v-btn
                color="primary mt-3"
                width="180"
                height="44"
                tile
                @click="$router.push('/login')"
                >Login</v-btn
              >
            </div>
          </v-col>
        </v-row>
      </v-responsive>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export type userData = {
  confirmationId: string
  email: string
  username: string
  id: string
}

export type DataType = {
  password: string
  confirmPassword: string
  isSend: boolean
  userData: userData
  errorMsg: string
}

export default Vue.extend({
  name: 'NewPassword',

  data() {
    return {
      password: '',
      confirmPassword: '',
      isSend: false,
      errorMsg: '',
      userData: {
        confirmationId: '',
        email: '',
      } as userData,
    } as DataType
  },

  created() {
    this.$axios
      .$get(`users/registration/confirm?id=${this.$route.params.token}`)
      .then((data) => {
        this.userData = data.user
      })
      .catch((err) => {
        console.log(err)
        this.$router.push('/login')
      })
  },

  methods: {
    signup(): void {
      const userName: string = this.userData.email.split('@')[0]
      if (this.password === this.confirmPassword) {
        this.$axios
          .$post('users/registration/confirm', {
            confirmation_id: this.userData.confirmationId,
            email: this.userData.email,
            username: userName,
            password: this.password,
            workspace: userName,
            additional_info: {},
          })
          .then((data) => {
            console.log(data)
            this.isSend = true
          })
          .catch((err) => {
            console.log(err)
          })
      } else {
        this.errorMsg = 'パスワードが一致しません'
      }
    },
  },
})
</script>
