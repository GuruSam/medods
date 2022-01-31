<template>
  <label class="checkbox">
    <input
      class="checkbox__input"
      type="checkbox"
      :checked="!value"
      @input="$emit('input', !$event.target.checked)"
    />

    <span class="checkbox__label">{{ label }}</span>
  </label>
</template>

<script>
export default {
  name: 'RadioButton',
  props: {
    label: String,
    value: Boolean,
    error: String
  }
}
</script>

<style scoped>
.checkbox {
  display: block;
}

.checkbox__input {
  margin-right: 10px;
}
</style>
