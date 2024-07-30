<template>
  <v-container class="about">
    <!-- Paginación -->
    <v-pagination
      v-if="pokedexInfo.results"
      v-model:page="page"
      :length="totalPages"
      circle
      class="my-4"
      @update:modelValue="handlePageChange"
    ></v-pagination>

    <!-- Lista de Pokémon -->
    <v-row class="pokemonBox" v-if="pokedexInfo.results && !error" justify="center">
      <v-col
        v-for="onePokemon in pokedexInfo.results"
        :key="onePokemon.id"
        cols="12"
        md="4"
        lg="3"
        @click="showMeThePokemon(onePokemon)"
      >
        <v-card :class="[onePokemon.types && onePokemon.types.length > 0 ? onePokemon.types[0].type.name : '', 'pokemon-card']">
          <v-img :src="onePokemon.image" :alt="onePokemon.name" class="white--text align-end" height="200px">
            <v-card-title>{{ onePokemon.name.toUpperCase() }}</v-card-title>
          </v-img>
          <v-card-subtitle>
            <div>
              <span>Tipo: </span>
              <strong v-for="oneType in onePokemon.types" :key="oneType.type.name">
                {{ oneType.type.name }} |
              </strong>
            </div>
            <span>Peso: {{ onePokemon.weight }} KG</span>
          </v-card-subtitle>
        </v-card>
      </v-col>
    </v-row>

    <!-- Paginación -->
    <v-pagination
      v-if="pokedexInfo.results"
      v-model:page="page"
      :length="totalPages"
      circle
      class="my-4"
      @update:modelValue="handlePageChange"
    ></v-pagination>
  </v-container>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      pokedexInfo: [],
      error: null,
      page: 1,
      perPage: 20, // Ajusta la cantidad de Pokémon por página
    }
  },

  computed: {
    totalPages() {
      return Math.ceil(this.pokedexInfo.count / this.perPage);
    }
  },

  methods: {
    async loadPokemonList(page = 1) {
      const offset = (page - 1) * this.perPage;
      const url = `https://pokeapi.co/api/v2/pokemon?offset=${offset}&limit=${this.perPage}`;

      try {
        const response = await axios.get(url);
        this.pokedexInfo = response.data;

        for (const onePokemon of this.pokedexInfo.results) {
          try {
            const pokemonResponse = await axios.get(onePokemon.url);
            const pokemonInfo = pokemonResponse.data;

            onePokemon.id = pokemonInfo.id;
            onePokemon.weight = pokemonInfo.weight;
            onePokemon.types = pokemonInfo.types;
            onePokemon.image = pokemonInfo.sprites.other.home.front_default;
          } catch (error) {
            console.error('Error al cargar la lista de Pokémon:', error);
            this.error = error.message || 'Ha ocurrido un error';
          }
        }
      } catch (error) {
        console.error('Error al cargar la lista de Pokémon:', error);
        this.error = error.message || 'Ha ocurrido un error';
      }
    },

    handlePageChange(newPage) {
      this.page = newPage;
      this.loadPokemonList(newPage);
    },

    showMeThePokemon(onePokemon) {
      this.$router.push({ name: 'pokemonInfo', params: { pokemonId: onePokemon.id } });
    }
  },

  mounted() {
    this.loadPokemonList();
  }
}
</script>

<style scoped>
.pokemon-card {
  background-color: #fff; 
  cursor: pointer;
  text-align: center;
  color: black;
  border-radius: 10px;
  margin: 20px;
  padding: 15px;
  box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.25);
  transition: all 0.2s ease-in-out;
}

.pokemon-card:hover {
  box-shadow: 0px 5px 10px rgba(0, 0, 0, 0.2);
  transform: translateY(-8px);
}

.pokemon-card .v-img {
  background-image: radial-gradient(circle at 10% 10%, #ffffff, transparent);
  border-radius: 50%;
  margin-bottom: 10px;
}

.pokemonBox {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
}

.v-pagination {
  display: flex;
  justify-content: center;
}
</style>
