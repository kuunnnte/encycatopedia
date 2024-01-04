<script>
import TheHero from './components/TheHero.vue';
import TheCatopedia from "./components/TheCatopedia.vue";
import TheFooter from './components/TheFooter.vue';

export default {
  components: { TheHero, TheCatopedia, TheFooter }
}
</script>

<template>
  <TheHero />
  <TheCatopedia />
  <TheFooter />
</template>

<style>
.loadMoreHidden {
  visibility: hidden;
}

.uk-border-rounded {
  border-radius: 1rem;
}
</style>
