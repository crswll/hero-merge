<template>
  <div class="hero-merge">
    <h1>Merge Two Heroes</h1>
    <p>Please choose two heros to merge</p>
    <div v-if="submitted && !validation['selected']" class="validation">
      You must choose two heros to merge them.
    </div>
    <ul>
      <li v-for="hero in heroes">
        <label>
          <input 
            v-model="selected"
            :value="hero"
            type="checkbox"
            :disabled="selected.length === 2 && selected.indexOf(hero) === -1"
          >
          {{ hero.hero_name }}
        </label>
        <span v-if="selected.indexOf(hero) > -1">
          <button @click="useAttributes(hero)">Use Attributes</button>
        </span>
      </li>
    </ul>
    <form @submit.prevent="save">
      <div class="field">
        <label for="superhero-alias">Super Hero Name</label>
        <input id="superhero-alias" placeholder="Mr. T" v-model="hero_name">
        <div v-if="submitted && !validation['hero_name']" class="validation">
          Hero name is required.
        </div>
      </div>
      <div class="field">
        <label for="superhero-name">Real Name</label>
        <input id="superhero-name" placeholder="Lawrence Tureaud" v-model="real_name">
        <div v-if="submitted && !validation['real_name']" class="validation">
          Real name is required.
        </div>
      </div>
      <div class="field">
        <label>Gender</label>
        <input id="superhero-gender" type="radio" value="Male" v-model="gender"> Male
        <input id="superhero-gender" type="radio" value="Female" v-model="gender">Female
      </div>
      <fieldset>
        <legend>Attributes</legend>
        <div v-for="(key, attribute) in attributes">
          <label :for="'attribute-' + key">{{ key | capitalize }}</label>
          <input :id="'attribute-' + key" v-model="attributes[key]" type="range" min="0" max="100" readonly>
          {{ attributes[key] }}
        </div>
      </fieldset>
      <fieldset>
        <legend>Powers</legend>
        <ul>
          <li v-for="power in possiblePowers">
            <label>
              <input
                v-model="powers"
                type="checkbox"
                :value="power"
                :disabled="powers.length === max_powers && powers.indexOf(power) === -1"
              >
              {{ power }}
            </label>
          </li>
        </ul>
      </fieldset>
      <fieldset>
        <legend>Weaknesses</legend>
        {{ weaknesses.join(', ') }}
      </fieldset>
      <button>Save</button>
    </form>
  </div>
</template>

<script>
  import api from './api'
  import bus from './bus'

  import ListManager from './ListManager.vue'

  export default {
    components: {
      ListManager,
    },
    created() {
      this.$http.get(api.url).then((response) => {
        this.heroes = response.data
      })
    },
    watch: {
      selected() {
        this.powers = this.powers.filter((power) => {
          return this.possiblePowers.indexOf(power) > -1
        })
        if (this.selected.length === 1) {
          this.useAttributes(this.selected[0])
        }
      }
    },
    computed: {
      weaknesses() {
        return this.selected.reduce((weaknesses, hero) => {
          return weaknesses.concat(hero.weaknesses)
        }, [])
      },
      possiblePowers() {
        return this.selected.reduce((powers, hero) => {
          return powers.concat(hero.powers)
        }, [])
      },
      form() {
        return {
          real_name: this.real_name,
          hero_name: this.hero_name,
          weaknesses: this.weaknesses,
          powers: this.powers,
          attributes: this.attributes,
          gender: this.gender,
        }
      },
      validation() {
        return {
          real_name: this.real_name.length > 0,
          hero_name: this.hero_name.length > 0,
          powers: this.powers.length > 0,
          selected: this.selected.length === 2
        }
      },
      isValid() {
        const valid = this.validation
        return Object.keys(valid).every((field) => {
          return valid[field] 
        })
      }
    },
    methods: {
      save() {
        this.submitted = true

        if (this.isValid && !this.saving) {
          this.saving = true
          this.$http.post(api.url, this.form).then((response) => {
            this.saving = false
            this.submitted = false
            this.clear()
            alert('Saved!')
            bus.$emit('getHeroes')
          })
        }
      },
      clear() {
        this.hero_name = ''
        this.real_name = ''
        this.gender = 'Male'
        this.powers = []
        this.weaknesses = []
        this.selected = []
        Object.keys(this.attributes).forEach((attribute) => {
          this.attributes[attribute] = 50
        })
      },
      useAttributes(hero) {
        Object.keys(hero.attributes).forEach((attribute) => {
          this.attributes[attribute] = hero.attributes[attribute]
        })
      }
    },
    data() {
      return {
        heroes: [],
        selected: [],
        saving: false,
        submitted: false,
        max_powers: 5,
        hero_name: '',
        real_name: '',
        gender: 'Male',
        attributes: {
          combat: 50,
          durability: 50,
          intelligence: 50,
          power: 50,
          speed: 50,
          strength: 50,
        },
        powers: [],
        weaknesses: [],
      }
    } 
  }
</script>

<style lang="sass">
  .hero-merge {
    margin-bottom: 1em;
    border: 1px solid #ccc;
    padding: 10px;
    fieldset {
      margin: 0 0 1.5em 0;
    }
    .validation {
      color: red; 
    }
    ul {
      list-style: none;
      padding: 0;
    }
  }

</style>