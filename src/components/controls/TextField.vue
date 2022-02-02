<template>
  <div class="text-field">
    <label class="text-field__label">
      {{ label }}
      <input
        class="text-field__input"
        :class="{'text-field__input--error' : error}"
        :type="type"
        :placeholder="placeholder"
        :value="value"
        @input="$emit('input', $event.target.value)"
      />
    </label>
    <span v-if="error" class="text-field__error">{{ error }}</span>
  </div>
</template>

<script>
export default {
  name: 'TextField',
  props: {
    label: String,
    type: String,
    placeholder: String,
    value: String,
    error: String
  }
}
</script>

<style scoped>
.text-field {
  position: relative;
}

.text-field__label {
  color: #756F86;
  font-weight: 500;
}

.text-field__input {
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

.text-field__input:focus {
  padding: 15px;
  border: 2px solid #0880AE;
}

.text-field__input:focus-visible {
  outline: none;
}

.text-field__input::placeholder {
  color: #7C9CBF;
}

.text-field__input--error {
  border: 1px solid #912222;
}

.text-field__error {
  position: absolute;
  bottom: -17px;
  padding: 0 6px;
  font-size: 13px;
  line-height: 18px;
  color: white;
  background-color: #cf3d3d;
  border-radius: 5px;
}
</style>
