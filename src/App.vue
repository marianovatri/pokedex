<template>
  <v-app>
    <v-app-bar app color="red" dark>
      <v-toolbar-title>Pokédex</v-toolbar-title>


      <v-spacer></v-spacer>
      
      <v-navigation-drawer v-model="drawer" app temporary>
        <v-list>
          <v-list-item @click="$router.push('/')">
            <v-list-item-content>
              <v-list-item-title>Inicio</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
          <v-list-item @click="$router.push('/about')">
            <v-list-item-content>
              <v-list-item-title>Acerca de</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
          <v-list-item @click="$router.push('/contact')">
            <v-list-item-content>
              <v-list-item-title>Contacto</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </v-navigation-drawer>
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
