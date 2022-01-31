<template>
  <div class="form">
    <h1 class="form__title">Создание клиента</h1>

    <form>
      <TextField class="form__field" v-model="formData.surname" label="Фамилия" type="text" placeholder="Введите фамилию" />
      <TextField class="form__field" v-model="formData.name" label="Имя" type="text" placeholder="Введите имя" />
      <TextField class="form__field" v-model="formData.middleName" label="Отчество" type="text" placeholder="Введите отчество" />
      <DateField class="form__field" v-model="formData.birthDate" label="Дата рождения" />
      <TextField class="form__field" v-model="formData.phone" label="Номер телефона" type="tel" placeholder="Введите номер телефона" />

      <div class="form__field">
        <span class="form__field-title">Пол</span>

        <RadioButton class="form__radio" v-model="formData.gender" label="Мужской" name="gender" selected-value="male" />
        <RadioButton class="form__radio" v-model="formData.gender" label="Женский" name="gender" selected-value="female" />
      </div>

      <Select class="form__field" v-model="formData.group" label="Группа клиентов" :options="clientGroups" multiple />
      <Select class="form__field" v-model="formData.doctor" label="Лечащий врач" :options="doctors" />
      <Checkbox class="form__field" label="Не отправлять СМС" v-model="formData.sms" />

      <!-- Second block -->
      <TextField class="form__field" v-model="formData.postalCode" label="Индекс" type="text" placeholder="Индекс" />
      <AutocompleteField class="form__field" v-model="formData.country" label="Страна" type="text" placeholder="Страна" :fetch-cb="onCountryInput" />
      <AutocompleteField class="form__field" v-model="formData.region" label="Область" type="text" placeholder="Область" :fetch-cb="onRegionInput" />
      <AutocompleteField class="form__field" v-model="formData.city" label="Город" type="text" placeholder="Город" :fetch-cb="onCityInput" />
      <AutocompleteField class="form__field" v-model="formData.street" label="Улица" type="text" placeholder="Улица" :fetch-cb="onStreetInput" />
      <TextField class="form__field" v-model="formData.house" label="Дом" type="text" placeholder="Дом" />
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

export default {
  name: 'Form',
  components: {
    TextField, DateField, RadioButton, Select, Checkbox, AutocompleteField
  },
  data: () => ({
    formData: {
      surname: '',
      name: '',
      middleName: '',
      birthDate: '',
      phone: '',
      gender: 'male',
      group: '',
      doctor: '',
      sms: true,
      postalCode: '',
      country: '',
      region: '',
      city: '',
      street: '',
      house: ''
    },
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
    errors: new Map()
  }),

  methods: {
    onCountryInput (value) {
      return this.fetchAutocomplete(value, 'country')
        .then(({ suggestions }) => suggestions.map(el => ({
          id: el.data.kladr_id,
          value: el.value
        })))
    },

    onRegionInput (value) {
      const restrictions = this.formData.country ? { country: this.formData.country } : {}

      return this.fetchAutocomplete(value, 'region', restrictions)
        .then(({ suggestions }) => suggestions.map(el => ({
          id: el.data.kladr_id,
          value: el.data.region_with_type
        })))
    },

    onCityInput (value) {
      const restrictions = this.formData.country ? { country: this.formData.country } : {}

      return this.fetchAutocomplete(value, 'city', restrictions)
        .then(({ suggestions }) => suggestions.map(el => ({
          id: el.data.kladr_id,
          value: el.data.city
        })))
    },

    onStreetInput (value) {
      const restrictions = this.formData.city ? { city: this.formData.city } : {}

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
