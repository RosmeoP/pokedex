<template>
  <div class="min-h-screen bg-gray-100 p-4">
    <header class="header max-w-md mx-auto bg-white p-6 rounded-lg shadow mb-8">
      <label class="block text-gray-700 mb-2">
        Type your pokemon name or id:
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

    <section class="header max-w-md mx-auto bg-white p-6 rounded-lg shadow mb-8">
      <label class="block text-gray-700 mb-2">
        Pokemon only gets generated between 1 and 151:
        <div class="flex mt-2">
          <button 
            @click="randomPokemonId"
            class="px-4 py-2 bg-blue-500 text-white rounded-r hover:bg-blue-600 transition-colors"
          >
            Buscar
          </button>
        </div>
        <!-- Mostrar mensaje de error si existe -->
        <p v-if="errorMessage" class="text-red-500 mt-2">{{ errorMessage }}</p>
      </label>
    </section>

    <main v-if="hasPokemonData" class="max-w-md mx-auto">
      <section class="pokemonCard bg-white rounded-xl shadow-md overflow-hidden">
        <div class="nameImg bg-gradient-to-r from-blue-400 to-blue-600 p-6 text-center">
          <h2 class="pokemonName text-2xl font-bold text-white capitalize">{{ pokemonData.name }}</h2>
          <img 
            :src="pokemonData.sprites.front_default" 
            :alt="pokemonData.name"
            class="mx-auto h-40 w-40 object-contain"
          />
        </div>

        <div class="types p-6 border-b">
          <h2 class="text-xl font-semibold mb-3 text-gray-700">Type:</h2>
          <ul class="flex flex-wrap gap-2">
            <li 
              v-for="(type, index) in pokemonData.types" 
              :key="index"
              class="px-4 py-1 rounded-full text-white capitalize"
              :class="getTypeClass(type.type.name)"
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

    <section class="max-w-5xl mx-auto mt-8">
      <h1 class="text-2xl font-bold mb-6 text-gray-800">Pokémon by Type</h1>
      <div class="grid grid-cols-2 md:grid-cols-3 gap-6">
        <!-- Fire Type Pokemon -->
        <div class="bg-white rounded-xl shadow-md p-7">
          <h2 class="text-xl font-semibold mb-4 text-red-500">Fire Type</h2>
          <div class="space-y-5" v-if="typePokemons.fire.length">
            <div v-for="pokemon in typePokemons.fire" :key="pokemon.name" 
                 class="flex items-center space-x-4 p-2 bg-gray-50 rounded">
              <img :src="pokemon.image" :alt="pokemon.name" class="w-17 h-16">
              <span class="capitalize">{{ pokemon.name }}</span>
            </div>
          </div>
          <div v-else class="text-gray-501">Loading...</div>
        </div>

        <!-- Water Type Pokemon -->
        <div class="bg-white rounded-xl shadow-md p-7">
          <h2 class="text-xl font-semibold mb-4 text-blue-500">Water Type</h2>
          <div class="space-y-5" v-if="typePokemons.water.length">
            <div v-for="pokemon in typePokemons.water" :key="pokemon.name" 
                 class="flex items-center space-x-4 p-2 bg-gray-50 rounded">
              <img :src="pokemon.image" :alt="pokemon.name" class="w-17 h-16">
              <span class="capitalize">{{ pokemon.name }}</span>
            </div>
          </div>
          <div v-else class="text-gray-501">Loading...</div>
        </div>

        <!-- Electric Type Pokemon -->
        <div class="bg-white rounded-xl shadow-md p-7">
          <h2 class="text-xl font-semibold mb-4 text-yellow-500">Electric Type</h2>
          <div class="space-y-5" v-if="typePokemons.electric.length">
            <div v-for="pokemon in typePokemons.electric" :key="pokemon.name" 
                 class="flex items-center space-x-4 p-2 bg-gray-50 rounded">
              <img :src="pokemon.image" :alt="pokemon.name" class="w-17 h-16">
              <span class="capitalize">{{ pokemon.name }}</span>
            </div>
          </div>
          <div v-else class="text-gray-501">Loading...</div>
        </div>
      </div>
    </section>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { pokeapi } from './api/pokeapi'
import axios from 'axios';

const pokemonId = ref('')
const pokemonData = ref({})
const errorMessage = ref('')

const hasPokemonData = computed(() => Object.entries(pokemonData.value).length > 0)

const getTypeClass = (typeName) => {
  const typeClasses = {
    fire: 'bg-red-500',
    water: 'bg-blue-500',
    grass: 'bg-green-500',
    electric: 'bg-yellow-400',
    poison: 'bg-purple-500',
    normal: 'bg-gray-400',
    fairy: 'bg-pink-300',
    fighting: 'bg-red-700',
    rock: 'bg-gray-600',
    flying: 'bg-indigo-300',
    bug: 'bg-lime-500',
    ground: 'bg-yellow-600',
    psychic: 'bg-pink-500',
    ghost: 'bg-purple-700',
    dragon: 'bg-indigo-600',
    dark: 'bg-gray-800',
    steel: 'bg-gray-500',
    ice: 'bg-blue-200'
  }
  return typeClasses[typeName] || 'bg-gray-400'
}

const searchPokemon = async () => {
  try {
    errorMessage.value = '' 
    const response = await axios.get(`${pokeapi}/${pokemonId.value.toLowerCase()}`);
    pokemonData.value = response.data; 
    console.log(pokemonData.value);
  } catch (error) {
    alert("No se encontró el pokemon");
    console.error("Error:", error.response?.data || error.message);
    pokemonData.value = {};
  }
}

const randomPokemonId = async () => {
  try {
    errorMessage.value = '' 
    const randomId = Math.floor(Math.random() * 200) + 1; 
    if (randomId > 151) {
      errorMessage.value = 'El Pokémon generado tiene un ID mayor a 151. Intenta nuevamente.' 
      pokemonData.value = {} 
      return
    }
    const response = await axios.get(`${pokeapi}/${randomId}`); 
    pokemonData.value = response.data; 
    console.log(pokemonData.value);
  } catch (error) {
    alert("No se encontró el pokemon");
    console.error("Error:", error.response?.data || error.message);
    pokemonData.value = {};
  }
}

const typePokemons = ref({
  fire: [],
  water: [],
  electric: []
})

const fetchPokemonByType = async (type) => {
  try {
    const response = await axios.get(`https://pokeapi.co/api/v2/type/${type}`);
    const pokemonList = response.data.pokemon.slice(0, 5);
    
    const detailedPokemon = await Promise.all(
      pokemonList.map(async (p) => {
        const pokemonData = await axios.get(p.pokemon.url);
        return {
          name: pokemonData.data.name,
          image: pokemonData.data.sprites.front_default
        };
      })
    );
    
    typePokemons.value[type] = detailedPokemon;
  } catch (error) {
    console.error(`Error fetching ${type} type pokemon:`, error);
    typePokemons.value[type] = [];
  }
}

onMounted(() => {
  fetchPokemonByType('fire');
  fetchPokemonByType('water');
  fetchPokemonByType('electric');
})
</script>