<template>
  <div>
    <Header :is-guest="false"></Header>
    <div>
      <v-responsive max-width="600" class="mx-auto text-center mt-16">
        <p class="text-h4">
          Welcome,
          {{
            $cookies.get('hexaUserData').username
              ? $cookies.get('hexaUserData').username
              : '新規ユーザー'
          }}<span class="text-h6">さん</span>
        </p>
      </v-responsive>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
  name: 'Page1',
})
</script>
