<template>
  <v-card class="modal">
    <v-row justify="center" align-content="center" style="height: 100%">
      <v-col cols="12">
        <p class="text-center font-weight-bold text-h4">UserProfile</p>
        <v-row justify="center">
          <v-col cols="6">
            <ValidationProvider v-slot="{ errors }" rules="email" name="email">
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
            <p class="text-decoration-underline" @click="close">キャンセル</p>
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
</template>

<script lang="ts">
import Vue from 'vue'

/* eslint-disable camelcase */
export type userData = {
  email: string
  username: string
  status: string
  i_id: string
  note: string
  user_id: { [user_id: string]: string }
}

export default Vue.extend({
  name: 'UserModal',

  data() {
    return {
      userData: {
        email: '',
        username: '',
        status: '',
        user_id: [{ user_id: '' }],
        i_id: '',
        note: '',
      },
      status: [
        {
          value: 'active',
          text: 'active',
        },
        {
          value: 'suspended',
          text: 'suspend',
        },
      ],
    }
  },

  created() {
    this.getUser()
  },

  methods: {
    close(): void {
      this.$emit('switchUserModal')
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

      if (this.userData.status === 'suspended') {
        items.action_id = 'suspendAccount'
      } else {
        items.action_id = 'active'
      }

      this.$axios
        .$post(
          `applications/samplelogin2/datastores/users/items/edit/${this.userData.i_id}`,
          items
        )
        .then((data) => {
          this.$cookies.set('userData', data.item)
          this.userData = data.item
          this.updateUserDataToHexabase()
        })
        .catch((err) => {
          console.log(err)
        })
    },

    getUser(): void {
      this.$axios
        .$post('applications/samplelogin2/datastores/users/items/search', {
          conditions: [
            {
              id: 'email',
              search_value: [this.$cookies.get('userData').email],
              exact_match: true,
            },
          ],
          page: 1,
          per_page: 0,
          use_display_id: true,
        })
        .then((data) => {
          this.userData = data.items[0]
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
          user_id: this.userData.user_id[0].user_id,
        })
        .then(() => {
          this.getUser()
          this.$emit('switchUserModal')
        })
        .catch((err) => {
          console.log(err)
        })
    },
  },
})
</script>
