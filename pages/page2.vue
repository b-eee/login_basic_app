<template>
  <div>
    <Header :is-guest="false" :name="userData.username"></Header>
    <div>
      <v-responsive max-width="600" class="mx-auto text-center mt-16">
        <p class="text-h4">Welcome to page 2</p>
      </v-responsive>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

/* eslint-disable camelcase */
export type userData = {
  username: string
}

export type DataType = {
  userData: userData
}

export default Vue.extend({
  name: 'Page2',

  data() {
    return {
      userData: {
        username: '',
      },
    } as DataType
  },

  created() {
    this.userData = this.$cookies.get('userData')
  },
})
</script>
