<template>
    <div class="mt-4 ">
        <div class="card" :style="{backgroundColor: backgroundcolor}" style="background: linear-gradient()">
            <div class="card-image">
                <figure>
                    <img :src="pokemonImg" style="margin-left:auto; margin-right:auto; padding-top: 5px" class="image is-128x128" alt="Placeholder image">
                </figure>
            </div>

            <div class="card-content">
                <div class="media" v-bind="mudarCorTipo()">
                <div class="media-content">
                    <p class="title is-4">{{pokemon.dexNumber}} - {{name | upper}}</p>
                    <button id="btnTipo"  :style="{backgroundColor: backgroundcolor}" class="button">
                        <p class="subtitle is-6">{{pokemon.type1}}</p></button>
                    <button id="btnTipo" :style="{backgroundColor: backgroundcolor2}" class="button" v-if="pokemon.type2">
                        <p class="subtitle is-6">{{pokemon.type2}}</p></button>
                </div>
                </div>

                <div class="content">
                    <button class="button is-fullwidth" @click="showEntry = true">Entries</button>
                </div>
                <div class="modal" v-bind:class="{'is-active': showEntry}">
                    <div class="modal-background" v-on:click="showEntry = false"></div>
                    <div class="modal-card">
                        <header class="modal-card-head" :style="{backgroundColor: backgroundcolor}">
                        <p class="modal-card-title">{{pokemon.nome}}</p>
                        <button class="delete" aria-label="close" v-on:click="showEntry = false"></button>
                        </header>
                        <section class="modal-card-body" >
                            <figure>
                                <img :src="currentImg" @mouseover="mudarSprite" @mouseleave="mudarSprite" alt="Placeholder image">
                                <img :src="currentShiny" @mouseover="mudarSpriteShiny" @mouseleave="mudarSpriteShiny" alt="Placeholder image">
                            </figure>
                            {{pokemon.description}}
                        </section>
                        <footer class="modal-card-foot" :style="{backgroundColor: backgroundcolor}">
                        <button class="button" v-on:click="showEntry = false">Cancel</button>
                        </footer>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    created: function(){
        axios.get(this.url).then(res => {
            
            this.pokemonImg = res.data.sprites.other.["official-artwork"].front_default; //aponta um erro de identificador
            this.pokemon.nome = res.data.forms[0].name;
            this.pokemon.type1 = res.data.types[0].type.name;
            if(res.data.types.length > 1){
                this.pokemon.type2 = res.data.types[1].type.name;
            }
            this.pokemon.front = res.data.sprites.front_default;
            this.pokemon.back = res.data.sprites.back_default;
            this.pokemon.frontShiny = res.data.sprites.front_shiny;
            this.pokemon.backShiny = res.data.sprites.back_shiny;
            this.currentImg = this.pokemon.front;
            this.currentShiny = this.pokemon.frontShiny;
            this.pokemon.dexNumber = res.data.id;


            axios.get(res.data.species.url).then(res => {
                for (let index = 0; index < res.data.flavor_text_entries.length; index++) {
                    if(res.data.flavor_text_entries[index].language.name == 'en'){
                        this.pokemon.description = res.data.flavor_text_entries[index].flavor_text
                    }
                }
                this.pokemon.generation = res.data.generation.name;
            });
        })
    },
    data(){
        return{
            showEntry: false,
            isFront: true,
            hover: false,
            pokemonImg: '',
            currentImg: '',
            currentShiny: '',
            backgroundcolor: '',
            backgroundcolor2: '',
            pokemon: {
                nome: '',
                type1: '',
                type2: '',
                front: '',
                back: '',
                frontShiny: '',
                backShiny: '',
                description: '',
                dexNumber: Number,
                generation: ''
            },
            tiposCor: {
                    normal: "#A8A878",
                    fighting: "#C03028",
                    flying: "#A890F0",
                    electric: "#F8D030",
                    ground: "#E0C068",
                    psychic: "#F85888",
                    rock: "#B8A038",
                    ice: "#98D8D8",
                    dragon: "#7038F8",
                    ghost: "#705898",
                    dark: "#705848",
                    steel: "#B8B8D0",
                    fairy: "#EE99AC",
                    grass: "#78C850",
                    fire: "#F08030",
                    water: "#6890F0",
                    poison: "#A040A0",
                    bug: "#A8B820",
            }
        }
    },
    props: {
        num: Number,
        name: String,
        url: String
    },
    filters: {
        upper: function(value) { //recebendo o texto, separando em duas partes, para colocar a primeira letra como maiuscula.
            var newName = value[0].toUpperCase() + value.slice(1);
            return newName;
        }
    },
    methods: {
        mudarSprite: function() {
            if(this.isFront){
                this.isFront = false;
                this.currentImg = this.pokemon.back;
            } else {
                this.isFront = true;
                this.currentImg = this.pokemon.front;
            }
        },
        mudarSpriteShiny: function(){
            if(this.isFront){
                this.isFront = false;
                this.currentShiny = this.pokemon.backShiny;
            } else {
                this.isFront = true;
                this.currentShiny = this.pokemon.frontShiny;
            }
            
        },
        mudarCorTipo: function(){
            for (var [key, value] of Object.entries(this.tiposCor)){
                if(this.pokemon.type1 === key){
                    this.backgroundcolor = value;
                }
                if(this.pokemon.type2 === key){
                    this.backgroundcolor2 = value;
                }
            }
        }
    }
}
</script>

<style>
.column-wrapper{
    column-count: 4;
}
 *:first-letter{
     text-transform: capitalize;
 }
 #btnTipo{
    border: 2px solid black; 
    pointer-events: none;
 }
</style>