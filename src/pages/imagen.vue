<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <v-card class="mx-auto" max-width="400">
    <img
      height="400"
      width="400"
      :src="urlImagen"
      alt="Imagen"
    >
    <p>{{ chiste }}</p>
    <v-card-actions>
      <v-btn color="black" @click="cambiaImagenYChiste">
        Cambiar
      </v-btn>
    </v-card-actions>
  </v-card>
</template>

<script setup>
import { ref } from 'vue';

const urlImagen = ref("https://picsum.photos/200/300");
const chiste = ref("");

async function cambiaImagenYChiste() {
  await cambiaImagen();
  await cambiaChiste();
}

async function cambiaImagen() {
  let respuesta = await fetch("https://picsum.photos/200/300");
  urlImagen.value = respuesta.url;
}

async function cambiaChiste() {
  let respuestaChiste = await fetch("https://api.chucknorris.io/jokes/random");
  let datosChiste = await respuestaChiste.json();
  chiste.value = datosChiste.value;
}

cambiaChiste();
</script>