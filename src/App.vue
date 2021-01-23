<template>
  <div id="app">
    <form class="form" >
      <div class="form-head">
        <div class="form-head__title">
          Регистрация
        </div>
        <div class="form-head__login">
          Уже есть аккаунт? <a href="#">Войти</a>
        </div>
      </div>
      <div class="form-main">
        <div class="form__input">
          <label for="name">Имя</label>
          <input
              type="text"
              id="name"
              placeholder="Введите Ваше имя"
              v-model="name"
              :class="{'is-invalid': $v.name.$error}"
              @input="$v.name.$touch()"
              @blur="$v.name.$touch()"
          >
          <div class="error" v-if="nameErr[0]">{{nameErr[0]}}</div>
        </div>
        <div class="form__input">
          <label for="email">Email</label>
          <input
              type="text"
              id="email"
              placeholder="Введите Ваше email"
              v-model="email"
              :class="{'is-invalid': $v.email.$error}"
              @input="$v.email.$touch()"
              @blur="$v.email.$touch()"
          >
          <div class="error" v-if="emailErr[0]">{{emailErr[0]}}</div>
        </div>
        <div class="form__input">
          <label for="phone">Номер телефона</label>
          <input
              type="text"
              id="phone"
              placeholder="Введите номер телефона"
              v-model="phone"
              :class="{'is-invalid': $v.phone.$error}"
              @input="$v.phone.$touch()"
              @blur="$v.phone.$touch()"
          >
          <div class="error" v-if="phoneErr[0]">{{phoneErr[0]}}</div>
        </div>
        <div class="form__select">
          <label for="lang">Язык</label>
          <div
              class="selected"
              @click="isSelected ? isSelected=false: isSelected=true"
              :class="{'dark' : isSelected, 'done' : langDone}"
          >
            {{ selectedLang }}
          </div>
          <div
              class="list"
              id="lang"
              v-if="isSelected"
          >
            <div
                class="list__item"
                v-for="(lang,i) in langs"
                :key="i"
                @click="changeLang(lang)"
            >
              {{ lang }}
            </div>
          </div>
        </div>
        <div class="form__checkbox">
          <input type="checkbox" id="policy" class="custom-checkbox" @click="isChecked = !isChecked">
          <label for="policy">Принимаю <a href="#">условия</a> использования</label>
        </div>
        <button
            type="submit"
            class="form__btn"
            :disabled="!isValid"
            :class="{'valid': isValid}"
        >
          Зарегистрироваться
        </button>
      </div>
    </form>
  </div>
</template>

<script>

import {   required, email } from 'vuelidate/lib/validators'

export default {
  name: 'App',
  data() {
    return {
      isSelected: false,
      langs: ['Русский', 'Английский', 'Китайский', 'Испанский'],
      selectedLang: 'Русский',
      langDone: false,
      name: null,
      email: null,
      phone: null,
      isChecked: false
    };
  },
  methods: {
    changeLang(lang) {
      this.selectedLang = lang;
      this.isSelected = false;
      this.langDone = true
    }
  },
  validations:{
    name:{
      required,
      validName: val => /^[а-яё -]*$/i.test(val),
    },
    email: {
      required,
      email,
    },
    phone:{
      required,
      /*проверка немного жесткая, но вроде работает*/
      validPhone: val => /^(\+7|7|8)!?[\-]?\(?[0-9]{3}\)?[\-]?[0-9]{3}[\-]?[0-9]{2}[\-]?[0-9]{2}$/.test(val),
    },
  },
  computed: {
    nameErr() {
      const errors = []
      const {$dirty, required, validName} = this.$v.name
      if ($dirty) {
        !required && errors.push('Введите, пожалуйста, Ваше имя')
        !validName && errors.push('Имя может состоять только из букв, пробелов и дефисов')

      }
      return errors
    },
    emailErr() {
      const errors = []
      const { $dirty, required, email } = this.$v.email
      if ($dirty) {
        !required && errors.push('Введите, пожалуйста, email')
        !email && errors.push('Неправильный формат email')
      }
      return errors
    },
    phoneErr() {
      const errors = []
      const { $dirty, required, validPhone } = this.$v.phone
      if ($dirty) {
        !required && errors.push('Введите, пожалуйста, номер телефона')
        !validPhone && errors.push('Неправильный формат телефона: +7(999)999-99-99')
      }
      return errors
    },
    isValid(){
      return !this.$v.$invalid && this.isChecked && this.langDone
    }
  }
};
</script>

<style lang="scss">
#app {
  font-family: 'IBM Plex Sans', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin-top: 60px;
  color: #2C2738;
}

.form {
  box-shadow: 0 12px 24px rgba(44, 39, 56, 0.02), 0 32px 64px rgba(44, 39, 56, 0.04);
  border-radius: 24px;
  padding: 40px 30px;

  &__input, &__select {
    display: flex;
    flex-direction: column;
    margin-bottom: 33px;
    position: relative;

    label {
      font-weight: 500;
      font-size: 16px;
      line-height: 21px;
      margin-bottom: 5px;
      color: #756f86;
    }

    input, .selected {
      padding: 15px;
      border: 1px solid #DBE2EA;
      box-shadow: 0 4px 8px rgba(44, 39, 56, 0.04);
      border-radius: 6px;
      color: #7c9cbf;
      font-size: 16px;
      line-height: 21px;

      &::placeholder {
        color: #7c9cbf
      }
    }

    .is-invalid{
      border: 2px solid #ff7171;
    }

    .selected {
      z-index: 1;
      position: relative;
      font-size: 16px;

      &:after {
        content: url('./assets/arrow.svg');
        position: absolute;
        right: 21px;
        top: 15px;
      }
    }

    .dark {
      border: 2px solid #0880AE;
      color: #2C2738;
    }
    .done{
      color: #2C2738;
    }

    .error{
      color: #ff7171;
      position: absolute;
      bottom: -25px;
      font-size: 14px;
      line-height: 18px;
    }

  }

  &__select {

    .list {
      position: absolute;
      background: #fff;
      width: 100%;
      z-index: 2;
      display: flex;
      flex-direction: column;
      border: 1px solid #DBE2EA;
      box-shadow: 0 4px 8px rgba(44, 39, 56, 0.04), 0 20px 20px rgba(44, 39, 56, 0.04);
      border-radius: 6px;
      top: 83px;

      &__item {
        padding: 12px 0 11px 15px;
        color: #756F86;

        &:hover {
          background-color: #ebf4f8;
        }
      }
    }
  }

  &__checkbox {
    margin-bottom: 33px;

    .custom-checkbox {
      position: absolute;
      z-index: -1;
      opacity: 0;

      + label {
        display: flex;
        align-items: center;

        &::before {
          content: '';
          display: inline-block;
          width: 28px;
          height: 28px;
          border: 1px solid #DBE2EA;
          box-shadow: 0 4px 8px rgba(44, 39, 56, 0.04);
          border-radius: 4px;
          margin-right: 8px;
          box-sizing: border-box;
        }

        a {
          margin: 0 3px;
        }
      }

      &:checked + label::before {
        border: 2px solid #0880AE;
        background-image: url('./assets/check.svg');
        background-repeat: no-repeat;
        background-position: center center;
        background-size: 50% 50%;
      }

    }

  }

  &__btn {
    width: 100%;
    padding: 18px;
    background: #DBE2EA;
    box-shadow: 0 2px 4px rgba(44, 39, 56, 0.08), 0 4px 8px rgba(44, 39, 56, 0.08);
    border-radius: 6px;
    border: none;
    font-weight: 500;
    font-size: 16px;
    line-height: 21px;
    color: #B1B5BF;
  }

  .valid{
    background: #0880AE;
    color: #EBF4F8;
  }
}

.form-head {
  margin-bottom: 55px;

  &__title {
    font-weight: bold;
    font-size: 34px;
    line-height: 44px;
    margin-bottom: 8px;
  }

  &__login {
    font-size: 16px;
    line-height: 22px;


  }
}


a {
  text-decoration: none;
  color: #0880ae;
}
</style>
