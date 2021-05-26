<template>
  <div id="app">
    
    <div class="column">
      <figure >
        <img src="./assets/pokemon-logo.png">
      </figure>
      
      <hr>
      <h4>Pokédex - João Gontijo</h4>
      <input class="input is-rounded mt-4" type="text" placeholder="Buscar Pokémon pelo nome" v-model="keyword">
      <button class="button is-fullwidth is-success mt-4" @click="checkName">Buscar</button>
      <div class="columns  is-multiline">
      <!--<div class="column is-one-quarter" v-for="(poke) in filteredPokemons" :key="poke.name">
        <Pokemon :name="poke.name" :url="poke.url" :num="poke.id+1"/>
      </div>-->
      <div class="column is-one-quarter" v-for="pokemon in filteredPoke" :key="pokemon.url">
        <Pokemon :name="pokemon.name" :url="pokemon.url" :num="pokemon.id+1"/>  
      </div>
      </div>
    </div>
    
  </div>
</template>

<script>
import axios from 'axios';
import {debounce} from "lodash";
import Pokemon from './components/Pokemon';
export default {
  name: 'App',
  data(){
    return {
      keyword: '',
      pokemons: [],
      filteredPokemons: [], //lista auxiliar de pokémon para busca
      busca: ''
    }
  },
  created: function(){ //assim que a requisição acabar, vai passar os dados para a função
    this.debounceName = debounce(this.checkName, 1000); //tempo para fazer a requisição do nome do pokemon quando digitado
    axios.get("https://pokeapi.co/api/v2/pokemon?limit=151&offset=0").then(res => {
      this.pokemons = res.data.results;
      this.filteredPokemons = res.data.results;
    })
  },
  components:{
    Pokemon
  },
  methods:{
    checkName(){
      console.log(`Checking name: ${this.keyword}`);
      this.filteredPokemons = this.pokemons;
      if(this.keyword == '' || this.busca == ' '){
        this.filteredPokemons = this.pokemons;
      } else {
        this.filteredPokemons = this.pokemons.filter(pokemon => pokemon.name == this.keyword.toLowerCase());
      }
    }
  },
  watch: {
    keyword(){
      if(!this.keyword) return;
      this.debounceName();
    }
  },
  computed:{
    filteredPoke: function(){
      return this.pokemons.filter((pokemon)=>{
        return pokemon.name.match(this.keyword.toLowerCase());
      })
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color: rgb(184, 73, 73);
}
</style>
