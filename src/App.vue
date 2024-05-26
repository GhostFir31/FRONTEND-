<template>
  <v-layout class="rounded rounded-md">
    <v-app-bar title="">
      <template v-slot:prepend>
         <v-app-bar-nav-icon @click.stop="barOpen = !barOpen" ></v-app-bar-nav-icon>
      </template>
      <v-spacer></v-spacer>
      <v-icon icon="mdi-logout"></v-icon>
    </v-app-bar>

    <v-navigation-drawer v-model="barOpen" floating>
      <v-divider></v-divider>
      <v-list-item prepend-icon="mdi-table"  link to="/tablaActivos" title="Activos"></v-list-item>
      <v-list-item prepend-icon="mdi-table" link to="/tablaResponsables"  title="Responsables"></v-list-item>
      <v-list-item prepend-icon="mdi-table" link to="/tablaUbicaciones" title="Ubicaciones"></v-list-item>
    </v-navigation-drawer>

    <v-main class="d-flex align-center justify-center mt-5" style="min-height: 300px;">
      <router-view></router-view>
    </v-main>
  </v-layout>
</template>

<script setup>
  import { ref } from 'vue';
  const barOpen = ref(false);
</script>