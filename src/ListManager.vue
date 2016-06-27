<template>
  <div class="list-manager">
    <ul class="list-manager__list">
      <li class="list-manager__item" v-for="item in list">
        {{ item }} <button @click="removeItem(item)" v-if="editable">X</button>
      </li>
    </ul>
    <template v-if="editable">
      <input v-model="newItem" @keypress.enter="addItem($event)" :placeholder="placeholder">
      <button @click="addItem($event)">+</button>
    </template>
  </div>
</template>

<script>
  export default {
    props: {
      list: {
        type: Array,
        required: true
      },
      placeholder: String,
      max: Number,
      editable: {
        type: Boolean,
        default: true
      }
    },
    data() {
      return {
        newItem: '',
      }
    },
    methods: {
      removeItem(item) {
        this.list.$remove(item)
      },
      addItem(event) {
        event.preventDefault()

        const item = this.newItem
        const list = this.list
        const max = this.max || Infinity

        if (
          item.length && 
          list.indexOf(item) === -1 && 
          list.length < max
        ) {
          this.list.push(item)
          this.newItem = ''
        }
      },
    }
  }
</script>

<style lang="sass">
  .list-manager {
    &__list {
      padding: 0;
      margin: 0;
      list-style: none;
    }
    &__item {
      margin: 5px 0;
    }
  }
</style>