<template>
  <div class="heroes-list">
    <hero-preview v-for="hero in heroes" :hero="hero"></hero-preview>
  </div>
</template>

<script>
  import api from './api'
  import bus from './bus'

  import HeroPreview from './HeroPreview.vue'

  export default {
    components: {
      HeroPreview
    },
    data() {
      return {
        heroes: []
      }
    },
    created() {
      this.getHeroes()
      bus.$on('getHeroes', this.getHeroes)
    },
    methods: {
      getHeroes() {
        this.$http.get(api.url).then((response) => {
          this.heroes = response.data
        })
      }
    }
  }
</script>