<template>
  <label class="select">
    {{ label }}
    <select class="select__input" :class="{ 'select__input--multiple' : multiple }" :multiple="multiple" @input="select">
      <option v-if="!multiple" value=""></option>
      <option v-for="option in options" :key="option.value" :value="option.value">{{ option.text }}</option>
    </select>
  </label>
</template>

<script>
export default {
  name: 'Select',
  props: {
    label: String,
    value: [String, Array],
    multiple: {
      type: Boolean,
      default: false
    },
    options: {
      type: Array,
      default: () => []
    },
    error: String
  },

  methods: {
    select(evt) {
      const selectedOptions = evt.target.selectedOptions
      const value = Array.from(selectedOptions).map(option => option.value)

      this.$emit('input', value.length > 1 ? value : value[0])
    }
  }
}
</script>

<style scoped>
.select {
  display: block;
}

.select__input {
  display: block;
  width: 100%;
  height: 40px;
  margin: 8px 0;
  padding: 8px;
  font-family: inherit;
  font-size: 14px;
  color: #2C2738;
  line-height: 21px;
  background: #FFFFFF;
  border: 1px solid #DBE2EA;
  box-shadow: 0px 4px 8px rgba(44, 39, 56, 0.04);
  border-radius: 4px;
}

.select__input--multiple {
  min-height: 80px;
  padding: 8px;
}

.select__input:focus {
  padding: 7px;
  border: 2px solid #0880AE;
}

.select__input:focus-visible {
  outline: none;
}
</style>
