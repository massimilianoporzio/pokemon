<!--<template>-->
<!--  <img alt="Vue logo" src="./assets/logo.png" />-->
<!--  <HelloWorld msg="CIAO Vue 3 + Vite" />-->
<!--</template>-->
<template>
 <pokemon-cards :pokemons="pokemons"
 @chosen="fetchEvolutionsBySpecies" :selected-id="selectedId"/>

  <pokemon-cards :pokemons="evolutions"/>


</template>


<script>

import PokemonCards from "./component/PokemonCards.vue";
const api = 'https://pokeapi.co/api/v2/pokemon'
const IDS =[698,172,728,7,885]


export default {
  data () {
    return {
      pokemons: [],
      evolutions: [],
      evolutionIDS: [],
      selectedId: null

    }
  },
  components: {

    PokemonCards
  },
  async created() {
   this.pokemons = await this.fetchData(IDS)
  },
  methods: {
    getIdFromUrl(url) {
     const splits = url.split( '/' );
     return parseInt(splits[splits.length-2])

    },
    getEvolutionsByEvolvesToArray(evolves_to) {

      if(evolves_to && evolves_to.length > 0)
      {
        const url = evolves_to[0].species.url
        this.evolutionIDS.push(this.getIdFromUrl(url) )
        this.getEvolutionsByEvolvesToArray(evolves_to[0].evolves_to)
      }
    },
    async fetchEvolutionsBySpecies(pokemon) {
     this.evolutions = []
      this.evolutionIDS = []
     const response =  await window.fetch(pokemon.species_url)
     const species = await response.json()
     const evochain_url =species.evolution_chain.url
     const evoChainResp = await window.fetch(evochain_url)
     const evoChain = await evoChainResp.json()
     const chain = evoChain.chain
     this.getEvolutionsByEvolvesToArray(chain.evolves_to)
     await this.fetchEvolutions(this.evolutionIDS)
      this.selectedId = pokemon.id

    },
    async fetchEvolutions (ids) {
     this.evolutions = await this.fetchData(ids)
    },
   async fetchData (ids) {
     const responses = await Promise.all(
         ids.map(id => window.fetch(`${api}/${id}`))
     )
     const json = await Promise.all(
         responses.map(data=>data.json())
     )

     return json.map(datum=>({
       id: datum.id,
       name: datum.name,
       sprite: datum.sprites.other['official-artwork'].front_default,
       types: datum.types.map(d => d.type.name),
       species_url: datum.species.url

     }))

   }
 }
}
</script>

<style scoped>

.cards {
  display: flex;
  width: 60%;
  margin: auto;
}


</style>


