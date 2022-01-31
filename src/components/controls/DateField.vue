<template>
  <label class="date-field">
    {{ label }}
    <input
      class="date-field__input"
      type="date"
      :value="value"
      @input="$emit('input', $event.target.value)"
    />
  </label>
</template>

<script>
export default {
  name: 'DateField',
  props: {
    label: String,
    value: String,
    error: String
  }
}
</script>

<style scoped>
.date-field {
  display: block;
  color: #756F86;
  font-weight: 500;
}

.date-field__input {
  display: block;
  width: 100%;
  height: 40px;
  margin: 8px 0;
  padding: 16px;
  font-family: inherit;
  font-size: 14px;
  color: #2C2738;
  line-height: 21px;
  background: #FFFFFF;
  border: 1px solid #DBE2EA;
  box-shadow: 0px 4px 8px rgba(44, 39, 56, 0.04);
  border-radius: 4px;
}

.date-field__input:focus {
  padding: 15px;
  border: 2px solid #0880AE;
}

.date-field__input:focus-visible {
  outline: none;
}
</style>
