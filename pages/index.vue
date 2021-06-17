<template>
  <div>
    <Header
      :is-guest="false"
      :name="userData.username"
      @logout="logout"
      @openUserModal="openUserModal"
    ></Header>
    <div>
      <v-responsive max-width="600" class="mx-auto text-center mt-16">
        <p class="text-h4">
          Welcome, {{ userData.username }}<span class="text-h6">さん</span>
        </p>
      </v-responsive>
    </div>
    <v-dialog v-model="userModal" width="680" persistent>
      <v-card class="modal">
        <v-row justify="center" align-content="center" style="height: 100%">
          <v-col cols="12">
            <p class="text-center font-weight-bold text-h4">UserProfile</p>
            <v-row justify="center">
              <v-col cols="6">
                <ValidationProvider
                  v-slot="{ errors }"
                  rules="email"
                  name="email"
                >
                  <v-text-field
                    v-model="userData.email"
                    label="email"
                    :error-messages="errors"
                  ></v-text-field>
                </ValidationProvider>
                <v-text-field
                  v-model="userData.username"
                  label="name"
                ></v-text-field>
                <v-text-field
                  v-model="userData.company"
                  label="company"
                ></v-text-field>
                <v-text-field
                  v-model="userData.department"
                  label="department"
                ></v-text-field>
                <v-select
                  :items="status"
                  item-text="text"
                  item-value="value"
                  label="status"
                ></v-select>
              </v-col>
            </v-row>
            <v-row class="text-center">
              <v-col cols="6">
                <p class="text-decoration-underline" @click="userModal = false">
                  キャンセル
                </p>
              </v-col>
              <v-col cols="6" class="pt-0">
                <v-btn
                  color="primary"
                  width="180"
                  height="44"
                  tile
                  @click="updateUserData()"
                  >Save</v-btn
                >
              </v-col>
            </v-row>
          </v-col>
        </v-row>
      </v-card>
    </v-dialog>
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
      status: [
        { text: 'new', value: 'new' },
        { text: 'signup completed', value: 'signup completed' },
        { text: 'suspended', value: 'suspended' },
      ],
    } as DataType
  },

  created() {
    this.$axios
      .$get('userinfo')
      .then((data) => {
        this.userData = data
        this.getItemId()
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

    openUserModal(): void {
      this.userModal = true
    },

    updateUserData(): void {
      this.$axios
        .$post(
          `applications/samplelogin2/datastores/users/items/edit/${this.item_id}`,
          {
            item: {
              email: this.userData.email,
              name: this.userData.username,
              status: this.userData.status,
              company: this.userData.company,
              department: this.userData.department,
              note: this.userData.note,
            },
            is_force_update: true,
            return_item_result: true,
          }
        )
        .then(() => {
          this.updateUserDataToHexabase()
        })
        .catch((err) => {
          console.log(err)
        })
    },

    updateUserDataToHexabase(): void {
      this.$axios
        .$put('userinfo', {
          email: this.userData.email,
          username: this.userData.username,
          user_id: this.userData.u_id,
        })
        .then(() => {
          this.userModal = false
        })
        .catch((err) => {
          console.log(err)
        })
    },

    getItemId(): void {
      this.$axios
        .$post('applications/samplelogin2/datastores/users/items/search', {
          conditions: [
            {
              id: 'email',
              search_value: [this.userData.email],
              exact_match: true,
            },
          ],
          page: 1,
          per_page: 0,
          use_display_id: true,
        })
        .then((data) => {
          if (data.totalItems === 0) {
            this.createUserDataToMyDB()
          } else {
            this.item_id = data.items[0].i_id
            this.userData.company = data.items[0].company
            this.userData.department = data.items[0].department
            this.userData.status = data.items[0].status
            this.userData.note = data.items[0].note
          }
        })
        .catch((err) => {
          console.log(err)
        })
    },

    createUserDataToMyDB(): void {
      this.$axios
        .$post('applications/samplelogin2/datastores/users/items/new', {
          item: {
            name: this.userData.username,
            email: this.userData.email,
            user_id: this.userData.u_id,
            status: 'signup completed',
          },
          return_item_result: true,
        })
        .catch((err) => {
          console.log(err)
        })
    },
  },
})
</script>
