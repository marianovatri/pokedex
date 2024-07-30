<template>
    <v-container class="pokemon-info" :class="pokemon.types && pokemon.types.length > 0 ? pokemon.types[0].type.name : ''">
      <v-btn to="/" class="mx-2">
        <v-icon left>mdi-arrow-left</v-icon>
        Volver al listado
      </v-btn>
  
      <v-alert v-if="error" type="error">{{ error }}</v-alert>
  
      <v-img v-if="pokemon.sprites" :src="pokemon.sprites.other.home.front_default" alt="Imagen del Pokémon" class="pokemon-image"></v-img>
  
      <v-card class="infoBox mx-auto" max-width="600">
        <v-card-title v-if="pokemon.name">{{ pokemon.name.toUpperCase() }}</v-card-title>
  
        <v-card-text>
          <div v-if="pokemon.description">
            <h3>Descripción</h3>
            <p>{{ pokemon.description }}</p>
          </div>
          <div>
            <h3>Detalles</h3>
            <p>Peso: {{ pokemon.weight }} KG</p>
            <p>
              Tipo:
              <strong v-for="oneType in pokemon.types" :key="oneType.type.name">
                {{ oneType.type.name }} |
              </strong>
            </p>
          </div>
        </v-card-text>
      </v-card>
  
      <v-card v-if="pokemon.moves" class="mx-auto mt-4" max-width="600">
        <v-card-title>Lista de movimientos</v-card-title>
        <v-card-text>
          <v-row class="buttonsBox">
            <v-btn v-if="start !== 0" @click="start -= 10; end -= 10" class="mx-2 bg-red">
              <v-icon left>mdi-arrow-left</v-icon>
              Anterior
            </v-btn>
            <v-btn v-if="pokemon.moves && pokemon.moves.slice(start, end).length === 10" @click="start += 10; end += 10" class="mx-2 bg-red">
              Siguiente
              <v-icon right>mdi-arrow-right</v-icon>
            </v-btn>
          </v-row>
          <v-row class="movesBox">
            <v-col v-for="(onePokeMove, idxMove) in pokemon.moves.slice(start, end)" :key="onePokeMove.move.name" cols="auto">
              <v-chip :style="getMoveStyle(idxMove)">
                {{ onePokeMove.move.name }}
              </v-chip>
            </v-col>
          </v-row>
        </v-card-text>
      </v-card>
    </v-container>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        pokemon: {},
        start: 0,
        end: 10,
        error: null
      };
    },
    methods: {
      getMoveStyle(idxMove) {
        const randomColorNumber = (idxMove * 0x5f6d2e) & 0xffffff;
        const randomColorHex = randomColorNumber.toString(16).padStart(6, '0');
        const randomColor = `#${randomColorHex}`;
  
        function getLuminance(hexColor) {
          const hex = hexColor.replace(/^#/, "");
          const r = parseInt(hex.slice(0, 2), 16);
          const g = parseInt(hex.slice(2, 4), 16);
          const b = parseInt(hex.slice(4, 6), 16);
          return (0.299 * r + 0.587 * g + 0.114 * b) / 255;
        }
  
        const luminanceThreshold = 0.7;
        const textColor = getLuminance(randomColor) < luminanceThreshold ? "#fff" : "#000";
  
        return { backgroundColor: randomColor, color: textColor };
      }
    },
    async mounted() {
      if (this.$route.params.pokemonId) {
        this.error = null;
  
        try {
          const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${this.$route.params.pokemonId}`);
          this.pokemon = response.data;
        } catch (error) {
          console.error('Error al cargar el Pokémon:', error);
          this.error = error.message || 'Ha ocurrido un error';
        }
  
        try {
          const response = await axios.get(`https://pokeapi.co/api/v2/characteristic/${this.$route.params.pokemonId}`);
          const characteristic = response.data;
  
          const spanishDescription = characteristic.descriptions.find(description => description.language.name === 'es').description;
          this.pokemon.description = spanishDescription;
        } catch (error) {
          console.error('Error al cargar la descripción del Pokémon:', error);
        }
      }
    }
  }
  </script>
  
  <style scoped>
  .pokemon-info {
    text-align: center;
    margin: 0 auto;
    width: 25em;
    padding: 20px;
    border-radius: 8px;
    background: #f5f5f5;
  }
  
  .pokemon-image {
    border-radius: 10px;
    width: 100%;
    max-width: 200px;
    margin: 20px auto;
    background-image: radial-gradient(circle at 50% 50%, #ffffff, transparent);
  }
  
  .infoBox {
    background: white;
    padding: 20px;
    border-radius: 10px;
    margin-top: 20px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  }
  
  .movesBox {
    display: flex;
    flex-wrap: wrap;
    background: #eaeaea;
    border-radius: 8px;
    justify-content: center;
    padding: 10px;
    margin-top: 20px;
  }
  
  .movesBox v-chip {
    margin: 5px;
    font-weight: 600;
    transition: all 0.2s ease-in-out;
  }
  
  .movesBox v-chip:hover {
    transform: translateY(-3px);
  }
  
  .buttonsBox {
    display: flex;
    justify-content: space-between;
    margin: 10px 0;
  }
  </style>
  