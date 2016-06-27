<template>
  <div class="hero-create">
    <form @submit.prevent="save">
      <h1>Create A New Super Hero</h1>
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
        <input id="gender-male" type="radio" value="Male" v-model="gender"> <label for="gender-male">Male</label>
        <input id="gender-female" type="radio" value="Female" v-model="gender"> <label for="gender-female">Female</label>
      </div>
      <fieldset>
        <legend>Attributes</legend>
        <div v-for="(key, attribute) in attributes">
          <label :for="'attribute-' + key">{{ key | capitalize }}</label>
          <input :id="'attribute-' + key"v-model="attributes[key]" type="range" min="0" max="100">
          {{ attributes[key] }}
        </div>
      </fieldset>
      <fieldset>
        <legend>Powers</legend>
        <list-manager :list.sync="powers" placeholder="Muscles"></list-manager>
        <div v-if="submitted && !validation['powers']" class="validation">We need at least one super power.</div>
      </fieldset>
      <fieldset>
        <legend>Weaknesses</legend>
        <list-manager :list.sync="weaknesses" placeholder="Pity"></list-manager>
        <div v-if="submitted && !validation['weaknesses']" class="validation">Everyone has at least one weakness.</div>
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
    computed: {
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
          weaknesses: this.weaknesses.length > 0,
          powers: this.powers.length > 0,
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
            bus.$emit('getHeros')
          })
        }
      },
      clear() {
        this.hero_name = ''
        this.real_name = ''
        this.gender = 'Male'
        this.attributes.combat = 50
        this.attributes.durability = 50
        this.attributes.intelligence = 50
        this.attributes.power = 50
        this.attributes.speed = 50
        this.attributes.strength = 50
        this.powers = []
        this.weaknesses = []
      }
    },
    data() {
      return {
        saving: false,
        submitted: false,
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
  .hero-create {
    margin-bottom: 1em;
    border: 1px solid #ccc;
    padding: 10px;
    fieldset {
      margin: 0 0 1.5em 0;
    }
    .validation {
      color: red; 
    }
  }

</style>