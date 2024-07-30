<template>
  <v-app>
    <v-app-bar app color="red" dark>
      <v-toolbar-title>Pokédex</v-toolbar-title>


      <v-spacer></v-spacer>
      
    </v-app-bar>

    <v-main>
      <v-container>
        <router-view></router-view>
      </v-container>
    </v-main>

  </v-app>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      drawer: false // Controla la visibilidad del menú lateral en dispositivos móviles
    }
  }
}
</script>

<style>
/* Estilos globales */
</style>
