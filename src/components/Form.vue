<template>
  <div class="form">
    <h1 class="form__title">Создание клиента</h1>

    <form>
      <TextField class="form__field" v-model="surname" label="Фамилия" type="text" placeholder="Введите фамилию" @input="validate('surname')" :error="errors.surname" />
      <TextField class="form__field" v-model="name" label="Имя" type="text" placeholder="Введите имя" @input="validate('name')" :error="errors.name" />
      <TextField class="form__field" v-model="middleName" label="Отчество" type="text" placeholder="Введите отчество" />
      <DateField class="form__field" v-model="birthDate" label="Дата рождения" @input="validate('birthDate')" :error="errors.birthDate" />
      <TextField class="form__field" v-model="phone" label="Номер телефона" type="tel" placeholder="Введите номер телефона" @input="validate('phone')" :error="errors.phone" />

      <div class="form__field">
        <span class="form__field-title">Пол</span>

        <RadioButton class="form__radio" v-model="gender" label="Мужской" name="gender" selected-value="male" />
        <RadioButton class="form__radio" v-model="gender" label="Женский" name="gender" selected-value="female" />
      </div>

      <Select class="form__field" v-model="group" label="Группа клиентов" :options="clientGroups" @input="validate('group')" :error="errors.group" multiple />
      <Select class="form__field" v-model="doctor" label="Лечащий врач" :options="doctors" />
      <Checkbox class="form__field" label="Не отправлять СМС" v-model="sms" />

      <!-- Second block -->
      <TextField class="form__field" v-model="postalCode" label="Индекс" type="text" placeholder="Индекс" />
      <AutocompleteField class="form__field" v-model="country" label="Страна" type="text" placeholder="Страна" :fetch-cb="onCountryInput" />
      <AutocompleteField class="form__field" v-model="region" label="Область" type="text" placeholder="Область" :fetch-cb="onRegionInput" />
      <AutocompleteField class="form__field" v-model="city" label="Город" type="text" placeholder="Город" :fetch-cb="onCityInput" @input="validate('city')" :error="errors.city" />
      <AutocompleteField class="form__field" v-model="street" label="Улица" type="text" placeholder="Улица" :fetch-cb="onStreetInput" />
      <TextField class="form__field" v-model="house" label="Дом" type="text" placeholder="Дом" />
    </form>
</div>
</template>

<script>
import TextField from './controls/TextField.vue'
import DateField from './controls/DateField.vue'
import RadioButton from './controls/RadioButton.vue'
import Select from './controls/Select.vue'
import Checkbox from './controls/Checkbox.vue'
import AutocompleteField from './controls/AutocompleteField.vue'

import { validationMixin } from 'vuelidate'
import { required } from 'vuelidate/lib/validators'

export default {
  name: 'Form',
  mixins: [validationMixin],
  components: {
    TextField, DateField, RadioButton, Select, Checkbox, AutocompleteField
  },

  data: () => ({
    surname: '',
    name: '',
    middleName: '',
    birthDate: '',
    phone: '',
    group: '',
    gender: 'male',
    doctor: '',
    sms: true,
    postalCode: '',
    country: '',
    region: '',
    city: '',
    street: '',
    house: '',
    clientGroups: [
      { text: 'VIP', value: 1 },
      { text: 'Проблемные', value: 2 },
      { text: 'ОМС', value: 3 }
    ],
    doctors: [
      { text: 'Иванов', value: 1 },
      { text: 'Захаров', value: 2 },
      { text: 'Чернышева', value: 3 }
    ],
    errors: {}
  }),

  validations: {
    surname: { required },
    name: { required },
    birthDate: { required },
    phone: { required },
    group: { required },
    city: { required }
  },

  methods: {
    onCountryInput (value) {
      return this.fetchAutocomplete(value, 'country')
        .then(({ suggestions }) => suggestions.map(el => ({
          id: el.data.kladr_id,
          value: el.value
        })))
    },

    onRegionInput (value) {
      const restrictions = this.country ? { country: this.country } : {}

      return this.fetchAutocomplete(value, 'region', restrictions)
        .then(({ suggestions }) => suggestions.map(el => ({
          id: el.data.kladr_id,
          value: el.data.region_with_type
        })))
    },

    onCityInput (value) {
      const restrictions = this.country ? { country: this.country } : {}

      return this.fetchAutocomplete(value, 'city', restrictions)
        .then(({ suggestions }) => suggestions.map(el => ({
          id: el.data.kladr_id,
          value: el.data.city
        })))
    },

    onStreetInput (value) {
      const restrictions = this.city ? { city: this.city } : {}

      return this.fetchAutocomplete(value, 'street', restrictions)
        .then(({ suggestions }) => suggestions.map(el => ({
          id: el.data.kladr_id,
          value: el.data.street_with_type
        })))
    },

    fetchAutocomplete (query, type, restrictions) {
      const options = {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Accept': 'application/json',
          'Authorization': 'Token ' + process.env.VUE_APP_DADATA_API_KEY
        },
        body: JSON.stringify({
          query,
          count: 5,
          'from_bound': { value: type },
          'to_bound': { value: type },
          locations: [{
            'country_iso_code': '*',
            ...restrictions
          }]
        })
      }

      return fetch(process.env.VUE_APP_DADATA_API_URL, options)
        .then(response => response.json())
    },

    validate(field) {
      const v = this.$v[field]
      v.$touch()

      if (v.$error) {
        if (!v.$required) {
          this.errors[field] = 'Поле должно быть заполнено'
        }
      } else {
        this.errors[field] = null
      }
    }
  }
}
</script>

<style>
label,
.form__field-title {
  font-size: 14px;
}

.container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  min-height: 100vh;
  background: #f0f0f0;
}

.form {
  width: 460px;
  margin: 0 auto;
  padding: 30px 30px;
  text-align: left;
  color: #2C2738;
  background: #FFFFFF;
  box-shadow: 0px 12px 24px rgba(44, 39, 56, 0.02), 0px 32px 64px rgba(44, 39, 56, 0.04);
  border-radius: 24px;
}

.form__title {
  margin: 0;
  margin-bottom: 8px;
  font-style: normal;
  font-weight: bold;
  font-size: 21px;
  line-height: 44px;
}

.form__field:not(:last-child) {
  margin-bottom: 20px;
}

.form__field-title {
  display: inline-block;
  width: 100%;
  margin-bottom: 10px;
  color: #756F86;
  cursor: default;
}

.form__radio:not(:last-child) {
  margin-right: 15px;
}

.form a {
  color: #0880AE;
  text-decoration: none;
}
</style>
