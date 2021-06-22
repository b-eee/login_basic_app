<template>
  <div>
    <Header
      :is-guest="false"
      :name="$cookies.get('userData').username"
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
    <v-dialog v-model="statusModal" width="680" persistent>
      <v-card class="modal">
        <v-row justify="center" align-content="center" style="height: 100%">
          <v-col cols="12">
            <p class="text-center font-weight-bold text-h4">UserProfile</p>
            <v-row justify="center">
              <v-col cols="6">
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
                <p
                  class="text-decoration-underline"
                  @click="statusModal = false"
                >
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
  i_id: string
  status: string
}

export type DataType = {
  headers: headers[]
  items: []
  userData: userData
  status: headers[]
  userStatus: string
  statusModal: boolean
}

export default Vue.extend({
  name: 'Index',

  data() {
    return {
      headers: [
        {
          text: 'name',
          value: 'username',
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
          value: 'active',
          text: 'active',
        },
        {
          value: 'suspended',
          text: 'suspended',
        },
      ],
      userData: {},
      userStatus: '',
      statusModal: false,
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
      })
      .catch((err) => {
        console.log(err)
      })
  },

  methods: {
    editUserData(item: any): void {
      this.userData = item
      this.statusModal = true
    },

    updateUserData(): void {
      const items = {
        item: {
          status: this.userData.status,
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
        .then(() => {
          this.statusModal = false
        })
        .catch((err) => {
          console.log(err)
        })
    },
  },
})
</script>
