<template>
  <div class="border-bottom font-weight-bold">
    <v-row class="pa-5 ma-0">
      <v-col cols="6">
        <router-link to="/" class="text-decoration-none">
          <p class="text-h5 ma-0 black--text">Sample App</p>
        </router-link>
      </v-col>
      <v-col v-if="isGuest" cols="6" class="text-right">
        <p
          class="mx-4 black--text d-inline text-decoration-underline"
          @click="execParentFunction('openSignUpModal')"
        >
          Sign up
        </p>
        <p
          class="mx-4 black--text d-inline text-decoration-underline"
          @click="execParentFunction('openLoginModal')"
        >
          Login
        </p>
      </v-col>

      <v-col v-else cols="6" class="text-right">
        <p
          class="mx-4 black--text d-inline text-decoration-underline"
          @click="logout"
        >
          Logout
        </p>
        <v-menu offset-y>
          <template #activator="{ on, attrs }">
            <p
              class="mx-4 black--text d-inline text-decoration-underline"
              v-bind="attrs"
              v-on="on"
            >
              {{ name }}
            </p>
          </template>
          <v-list>
            <v-list-item>
              <v-list-title @click="switchUserModal">
                User Profile
              </v-list-title>
            </v-list-item>
            <v-list-item>
              <v-list-title @click="switchChangePasswordModal">
                Change Password
              </v-list-title>
            </v-list-item>
            <v-list-item v-if="$cookies.get('is_ws_admin')">
              <v-list-title @click="pushAdminUsers"> Admin User </v-list-title>
            </v-list-item>
          </v-list>
        </v-menu>
      </v-col>
    </v-row>
    <v-row v-if="!isGuest" class="mt-4 mb-4">
      <v-col cols="2" offset="1"> page1 </v-col>
      <v-col cols="2"> page2 </v-col>
      <v-col cols="2"> page3 </v-col>
      <v-col cols="2"> page4 </v-col>
      <v-col cols="2"> page5 </v-col>
    </v-row>

    <v-dialog v-model="changePasswordModal" width="680" persistent>
      <ChangePasswordModal
        @switchChangePasswordModal="switchChangePasswordModal"
      ></ChangePasswordModal>
    </v-dialog>

    <v-dialog v-model="userModal" width="680" persistent class="oveflow-hidden">
      <UserModal @switchUserModal="switchUserModal"></UserModal>
    </v-dialog>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import UserModal from './UserModal.vue'
import ChangePasswordModal from './ChangePasswordModal.vue'

export default Vue.extend({
  name: 'Header',

  components: {
    UserModal,
    ChangePasswordModal,
  },

  props: {
    isGuest: {
      type: Boolean,
      default: false,
    },
    name: {
      type: String,
      default: '',
    },
  },

  data() {
    return {
      changePasswordModal: false,
      userModal: false,
    }
  },

  methods: {
    execParentFunction(functionName: string): void {
      this.$emit(functionName)
    },

    switchChangePasswordModal(): void {
      this.changePasswordModal = !this.changePasswordModal
    },

    switchUserModal(): void {
      console.log('aaa')
      this.userModal = !this.userModal
    },

    pushAdminUsers(): void {
      this.$router.push('/users')
    },

    logout(): void {
      this.$cookies.remove('token')
      this.$router.push('/login')
    },
  },
})
</script>

<style scoped>
.border-bottom {
  border-bottom: #dedede solid 1px;
}

.overflow-hidden {
  overflow: hidden;
}
</style>
