<template>
  <div>
    <Header
      :is-guest="false"
      :name="userData.username"
      @logout="logout"
    ></Header>
    <div>
      <v-responsive max-width="600" class="mx-auto text-center mt-16">
        <p class="text-h4">
          Welcome, {{ userData.username }}<span class="text-h6">さん</span>
        </p>
      </v-responsive>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

/* eslint-disable camelcase */
export type userData = {
  email: string
  username: string
  company: string
  department: string
  status: string
  u_id: string
  note: string
}

export type DataType = {
  userModal: boolean
  item_id: string
  userData: userData
}

export default Vue.extend({
  name: 'Index',

  data() {
    return {
      userModal: false,
      item_id: '',
      userData: {
        u_id: '',
        email: '',
        username: '',
        company: '',
        department: '',
        status: '',
        note: '',
      },
    } as DataType
  },

  created() {
    this.$axios
      .$get('userinfo')
      .then((data) => {
        this.userData = data
      })
      .catch((err) => {
        console.log(err)
      })
  },

  methods: {
    logout(): void {
      this.$cookies.remove('token')
      this.$router.push('/login')
    },
  },
})
</script>
