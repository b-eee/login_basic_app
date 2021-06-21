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
          @click="execParentFunction('logout')"
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
            <v-list-item v-for="(item, index) in items" :key="index">
              <v-list-item-title @click="execParentFunction(item.action)">
                {{ item.title }}
              </v-list-item-title>
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
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  name: 'Header',

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
      items: [
        { title: 'User Profile', action: 'openUserModal' },
        { title: 'Change Password', action: 'openChangePasswordModal' },
        { title: 'Admin Users', action: 'pushAdminUsers' },
      ],
    }
  },

  methods: {
    execParentFunction(functionName: string): void {
      console.log(functionName)
      this.$emit(functionName)
    },
  },
})
</script>

<style scoped>
.border-bottom {
  border-bottom: #dedede solid 1px;
}
</style>
