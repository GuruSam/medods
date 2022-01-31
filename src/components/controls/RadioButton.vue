<template>
  <label class="radio-button">
    <input
      class="radio-button__input"
      type="radio"
      :name="name"
      :value="selectedValue"
      @input="$emit('input', $event.target.value)"
    />

    <span class="radio-button__label">{{ label }}</span>
  </label>
</template>

<script>
export default {
  name: 'RadioButton',
  props: {
    label: String,
    selectedValue: String,
    name: String,
    error: String
  }
}
</script>

<style scoped>

</style>
