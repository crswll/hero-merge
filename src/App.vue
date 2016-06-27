<template>
  <div id="app">
    <ul>
      <li><a href="#create">Create or Merge</a></li>
      <li><a href="#list">View Heroes</a></li>
    </ul>
    <div class="layout-split-1-2" id="create">
      <div class="col">
        <hero-merge></hero-merge>
      </div>
      <div class="col">
        <hero-create></hero-create>
      </div>
    </div>
    <div id="list">
      <h1>List Heroes</h1>
      <hero-list></hero-list>
    </div>
  </div>
</template>

<script>
  import HeroList from './HeroList.vue'
  import HeroCreate from './HeroCreate.vue'
  import HeroMerge from './HeroMerge.vue'

  export default {
    components: {
      HeroList,
      HeroCreate,
      HeroMerge,
    }
  }
</script>

<style lang="sass">
  *, *::before, *::after {
    box-sizing: border-box;
  }

  .layout-split-1-2 {
    display: flex;
    .col {
      flex: 1;
      padding: 10px;
    }
  }

  body {
    font-family: Helvetica, sans-serif;
  }
</style>
