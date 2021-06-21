<template>
  <div>
    <Header
      :is-guest="false"
      :name="$cookies.get('userData').username"
      @logout="logout"
    ></Header>
    <div>
      <v-responsive class="mx-10 mt-16">
        <p class="text-h4">Users list</p>

        <v-row>
          <v-col cols="12">
            <v-data-table :headers="headers" :items="items">
              <template #[`item.edit`]="{ item }">
                <img src="/edit.png" @click="editUserData(item)" />
              </template>
            </v-data-table>
          </v-col>
        </v-row>
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
                <v-textarea
                  v-model="userData.note"
                  solo
                  name="input-7-4"
                  label="note"
                ></v-textarea>
                <v-select
                  v-model="userData.status"
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

export type headers = {
  text: string
  value: string
}

/* eslint-disable camelcase */
export type userData = {
  email: string
  username: string
  status: string
  note: string
  i_id: string
  user_id: string
}

export type DataType = {
  headers: headers[]
  items: []
  userModal: boolean
  userData: userData
  status: headers[]
}

export default Vue.extend({
  name: 'Index',

  data() {
    return {
      headers: [
        {
          text: 'name',
          value: 'name',
        },
        {
          text: 'email',
          value: 'email',
        },
        {
          text: 'status',
          value: 'status',
        },
        {
          text: 'note',
          value: 'note',
        },
        {
          text: '編集',
          value: 'edit',
          sortable: false,
        },
      ],
      items: [],
      status: [
        {
          value: 'signup completed',
          text: 'signup completed',
        },
        {
          value: 'suspend',
          text: 'suspend',
        },
      ],
      userData: {},
      userModal: false,
    } as DataType
  },

  created() {
    this.$axios
      .$post('applications/samplelogin2/datastores/users/items/search', {
        conditions: [],
        page: 1,
        per_page: 0,
        use_display_id: true,
      })
      .then((data) => {
        this.items = data.items
        console.log(this.items)
      })
      .catch((err) => {
        console.log(err)
      })
  },

  methods: {
    editUserData(item: object): void {
      this.userData = item
      this.userModal = true
    },

    updateUserData(): void {
      const items = {
        item: {
          email: this.userData.email,
          username: this.userData.username,
          status: this.userData.status,
          note: this.userData.note,
        },
        is_force_update: true,
        return_item_result: true,
        action_id: '',
      }

      if (this.userData.status === 'suspend') {
        items.action_id = 'suspendAccount'
      } else {
        items.action_id = 'signupCompleted'
      }

      this.$axios
        .$post(
          `applications/samplelogin2/datastores/users/items/edit/${this.userData.i_id}`,
          items
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
          user_id: this.userData.user_id,
        })
        .then(() => {
          this.$cookies.set('userData', this.userData)
          this.userModal = false
        })
        .catch((err) => {
          console.log(err)
        })
    },
  },
})
</script>
