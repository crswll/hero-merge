<template>
  <div class="hero-preview">
    <h1 class="hero-preview__name">
      {{ hero.hero_name }}
      <em class="hero-preview__real-name">({{ hero.real_name }})</em>
    </h1>
    <ul class="hero-preview__list">
      <li class="hero-preview__list-item">Powers:</li>
      <li class="hero-preview__list-item" v-for="power in hero.powers">{{ power }}</li>
    </ul>
    <ul class="hero-preview__list">
      <li class="hero-preview__list-item">Weaknesses:</li>
      <li class="hero-preview__list-item" v-for="weakness in hero.weaknesses">{{ weakness }}</li>
    </ul>
    <button @click="delete">Delete Hero</button>
  </div>
</template>

<script>
  import api from './api'
  import bus from './bus'

  export default {
    props: {
      hero: Object
    },
    methods: {
      delete() {
        const id = this.hero.id
        if (confirm('Are you sure you want to delete this?')) {
          this.$http.delete(api.url + id).then((response) => {
            bus.$emit('getHeroes')
          })
        }
      }
    }
  }
</script>

<style lang="sass">
  .hero-preview {
    margin: 0 0 1em 0;
    &__name {
      font-size: 1em;
      margin: 0;
    }
    &__list {
      margin: 5px 0;
      padding: 0;
      list-style: none;
      color: #666;
      font-size: 0.85em;
    }
    &__list-item {
      display: inline;
      margin-right: 0;
      &::after {
        content: ', ';
        display: inline;
      }
      &:first-child {
        font-weight: bold;
      }
      &:last-child::after,
      &:first-child::after {
        content: ''
      }
    }
  }
</style>