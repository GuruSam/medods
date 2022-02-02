<template>
  <div class="autocomplete">
    <TextField v-model="textValue" :label="label" :type="type" :placeholder="placeholder" :error="error" @input="onInput" />

    <ul class="autocomplete__options" v-if="showOptions">
      <li
        class="autocomplete__option"
        v-for="option in options"
        :key="option.id"
        @click.prevent="onOptionClick"
      >
          {{ option.value}}
      </li>
    </ul>
  </div>
</template>

<script>
import TextField from './TextField.vue'

export default {
  name: 'AutocompleteField',

  props: {
    label: String,
    type: String,
    placeholder: String,
    value: String,
    fetchCb: {
      type: Function,
      default: () => {}
    },
    error: String
  },

  components: {
    TextField
  },

  data: () => ({
    options: [],
    showOptions: false
  }),

  mounted() {
    document.addEventListener('click', () => {
      if (this.showOptions) {
          this.showOptions = false
      }
    })
  },

  computed: {
    textValue: {
      get() {
        return this.value
      },
      set(value) {
        this.$emit('input', value)
      }
    }
  },

  methods: {
    onInput (value) {
      if (value) {
        this.fetchCb(value)
          .then(data => {
            this.options = data
            this.showOptions = this.options.length ? true : false
          })
      }
    },

    onOptionClick(evt) {
      this.$emit('input', evt.target.innerText)
      this.showOptions = false
    }
  }
}
</script>

<style>
.autocomplete {
  position: relative;
}

.autocomplete__options {
  position: absolute;
  top: 55px;
  left: 0;
  z-index: 1;
  width: 100%;
  padding: 12px 0;
  background: #FFFFFF;
  border: 1px solid #DBE2EA;
  box-shadow: 0px 4px 8px rgba(44, 39, 56, 0.04), 0px 20px 20px rgba(44, 39, 56, 0.04);
  border-radius: 6px;
  list-style: none;
  user-select: none;
}

.autocomplete__option {
  padding: 12px 0 12px 15px;
  font-size: 16px;
  line-height: 21px;
  color: #756F86;
  cursor: pointer;
}

.autocomplete__option:hover {
  background: #EBF4F8;
}
</style>
