<template>
  <div>
    <Header :is-guest="false" :name="userData.username"></Header>
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
  item_id: string
  userData: userData
}

export default Vue.extend({
  name: 'Index',

  data() {
    return {
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
    this.userData = this.$cookies.get('userData')
    console.log(this.userData)
  },

  methods: {
    openUserModal(): void {
      this.userModal = true
    },

    updateUserData(): void {
      const items = {
        item: {
          email: this.userData.email,
          username: this.userData.username,
          status: this.userData.status,
          company: this.userData.company,
          department: this.userData.department,
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
          `applications/samplelogin2/datastores/users/items/edit/${this.item_id}`,
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
          user_id: this.userData.u_id,
        })
        .then(() => {
          this.userModal = false
        })
        .catch((err) => {
          console.log(err)
        })
    },

    // getItemId(): void {
    //   this.$axios
    //     .$post('applications/samplelogin2/datastores/users/items/search', {
    //       conditions: [
    //         {
    //           id: 'email',
    //           search_value: [this.userData.email],
    //           exact_match: true,
    //         },
    //       ],
    //       page: 1,
    //       per_page: 0,
    //       use_display_id: true,
    //     })
    //     .then((data) => {
    //       if (data.totalItems === 0) {
    //         this.createUserDataToMyDB()
    //       } else {
    //         this.item_id = data.items[0].i_id
    //         this.userData.company = data.items[0].company
    //         this.userData.department = data.items[0].department
    //         this.userData.note = data.items[0].note
    //         this.userData.status = data.items[0].status
    //       }
    //     })
    //     .catch((err) => {
    //       console.log(err)
    //     })
    // },

    createUserDataToMyDB(): void {
      this.$axios
        .$post('applications/samplelogin2/datastores/users/items/new', {
          item: {
            username: this.userData.username,
            email: this.userData.email,
            user_id: this.userData.u_id,
          },
          return_item_result: true,
        })
        .then((data) => {
          this.item_id = data.item_id
        })
        .catch((err) => {
          console.log(err)
        })
    },

    switchChangePasswordModal(): void {
      this.changePasswordModal = !this.changePasswordModal
    },
  },
})
</script>
