<template>
  <div class="cards" >
    <div style="display: flex">
      <Card v-for="pokemon in pokemons"
            :key="pokemon.id" @click="click(pokemon)"
            :class="{opace: selectedId && pokemon.id !== selectedId}" class="card">

        <template v-slot:title>
          {{ pokemon.name }} #{{pokemon.id}}
        </template>

        <template v-slot:content>
          <img :src="pokemon.sprite" :alt="`Image of ${pokemon.name}`">
        </template>

        <template v-slot:description>
          <div v-for="(tipo,index) in pokemon.types" :key="index">
            {{tipo}}
          </div>
        </template>

      </Card>
    </div>

  </div>

</template>

<script>
import Card from './Card.vue';
export default {
  name: "PokemonCards",
  components: {
    Card,
  },
  methods: {
    click (pokemon) {
      this.$emit('chosen',pokemon)
    }
  },
  props: {
    pokemons: {
      type: Array,
      default: []
    },
    selectedId: {
      type: Number,
      required: false
    }
  }
}
</script>

<style scoped>
.card:hover {
  opacity: 1.0;
}
.opace {
  opacity: 0.5;
}
img {
  width: 100%;
}
</style>
