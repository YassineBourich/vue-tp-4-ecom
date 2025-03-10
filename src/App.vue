<template>
  <!--img alt="Vue logo" src="./assets/logo.png"-->
  <Filters />
  <Fiches />
  <Panier />
</template>

<script>
import Fiches from './components/Fiches.vue'
import Filters from './components/Filters.vue';
import Panier from './components/Panier.vue';

export default {
  name: 'App',
  components: {
    Filters,
    Fiches,
    Panier
  }
}
</script>

<style>

</style>
