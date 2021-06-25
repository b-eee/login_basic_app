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
              {{
                $cookies.get('hexaUserData').username
                  ? $cookies.get('hexaUserData').username
                  : '新規ユーザー'
              }}
            </p>
          </template>
          <v-list>
            <v-list-item>
              <p @click="switchUserModal">User Profile</p>
            </v-list-item>
            <v-list-item>
              <p @click="switchChangePasswordModal">Change Password</p>
            </v-list-item>
            <v-list-item v-if="$cookies.get('is_ws_admin')">
              <p @click="pushAdminUsers">Admin User</p>
            </v-list-item>
          </v-list>
        </v-menu>
      </v-col>
    </v-row>
    <v-row v-if="!isGuest" class="mt-4 mb-4">
      <v-col cols="1"></v-col>
      <v-col v-for="i of 5" :key="i" cols="2">
        <router-link :to="`/page${i}`" class="text-decoration-none black--text"
          >page{{ i }}</router-link
        >
      </v-col>
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
