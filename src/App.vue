<template>
  <div style="margin-top: 270px;">
    <v-card class="mx-auto px-15 py-15" max-width="344">
      <BtnGoogleLogin></BtnGoogleLogin>
    </v-card>
  </div>
</template>

<script>

import BtnGoogleLogin from '@/components/BtnGoogleLogin.vue';

export default {
  components: {
    BtnGoogleLogin,
  },
};
</script>

<style scoped>
  .v-card {
    border-radius: 10px;
  }
</style>
