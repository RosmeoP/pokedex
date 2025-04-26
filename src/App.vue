<template>
  <div class="min-h-screen bg-gray-100 p-4">
    <header class="header max-w-md mx-auto bg-white p-6 rounded-lg shadow mb-8">
      <label class="block text-gray-700 mb-2">
        Type your pokemon name:
        <div class="flex mt-2">
          <input 
            v-model="pokemonId"
            placeholder="type a name"
            type="text"
            class="flex-1 px-4 py-2 border border-gray-300 rounded-l focus:outline-none focus:ring-2 focus:ring-blue-500"
            @keyup.enter="searchPokemon"
          >
          <button 
            @click="searchPokemon"
            class="px-4 py-2 bg-blue-500 text-white rounded-r hover:bg-blue-600 transition-colors"
          >
            Buscar
          </button>
        </div>
      </label>
    </header>

    <!-- Tarjeta de Pokémon -->
    <main v-if="Object.entries(pokemonData).length > 0" class="max-w-md mx-auto">
      <section class="pokemonCard bg-white rounded-xl shadow-md overflow-hidden">
        <!-- Nombre e Imagen -->
        <div class="nameImg bg-gradient-to-r from-blue-400 to-blue-600 p-6 text-center">
          <h2 class="pokemonName text-2xl font-bold text-white capitalize">{{ pokemonData.name }}</h2>
          <img 
            :src="pokemonData.sprites.front_default" 
            :alt="pokemonData.name"
            class="mx-auto h-40 w-40 object-contain"
          />
        </div>

        <!-- Tipos -->
        <div class="types p-6 border-b">
          <h2 class="text-xl font-semibold mb-3 text-gray-700">Type:</h2>
          <ul class="flex flex-wrap gap-2">
            <li 
              v-for="(type, index) in pokemonData.types" 
              :key="index"
              class="px-4 py-1 rounded-full text-white capitalize"
              :class="{
                'bg-red-500': type.type.name === 'fire',
                'bg-blue-500': type.type.name === 'water',
                'bg-green-500': type.type.name === 'grass',
                'bg-yellow-400': type.type.name === 'electric',
                'bg-purple-500': type.type.name === 'poison',
                'bg-gray-400': type.type.name === 'normal',
                'bg-pink-300': type.type.name === 'fairy',
                'bg-red-700': type.type.name === 'fighting',
                'bg-gray-600': type.type.name === 'rock',
                'bg-indigo-300': type.type.name === 'flying',
                'bg-lime-500': type.type.name === 'bug',
                'bg-yellow-600': type.type.name === 'ground',
                'bg-pink-500': type.type.name === 'psychic',
                'bg-purple-700': type.type.name === 'ghost',
                'bg-indigo-600': type.type.name === 'dragon',
                'bg-gray-800': type.type.name === 'dark',
                'bg-gray-500': type.type.name === 'steel',
                'bg-blue-200': type.type.name === 'ice'
              }"
            >
              {{ type.type.name }}
            </li>
          </ul>
        </div>

        <div class="p-6">
          <h2 class="text-xl font-semibold mb-3 text-gray-700">Stats</h2>
          <ul class="space-y-2">
            <li 
              v-for="(stat, index) in pokemonData.stats" 
              :key="index"
              class="flex justify-between items-center"
            >
              <span class="capitalize text-gray-600">{{ stat.stat.name }}:</span>
              <span class="font-bold">{{ stat.base_stat }}</span>
            </li>
          </ul>
        </div>
      </section>
    </main>
  </div>
</template>

<script>
import { pokeapi } from './api/pokeapi'

export default {
  name: "App", 

  data(){
    return{
      pokemonData: {},
      pokemonId: "",
    }
  },

  methods:{
    async searchPokemon(){
      try {
        const pokemonToFind = await fetch(`${pokeapi}/${this.pokemonId}`)
        const pokemon = await pokemonToFind.json()
        this.pokemonData = pokemon 
        console.log(this.pokemonData)
        return pokemon
      } catch (error) {
        alert("No se encontró el pokemon")
        console.log(error)
      } 
    } 
  }
}
</script>