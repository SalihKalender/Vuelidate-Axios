<template>
  <form @submit.prevent="Submit">
      <div class="form-group">
        <label>Username</label>
        <input type="text" v-model.lazy="userForm.userName" @blur="$v.userForm.userName.$touch()">
        <small v-if="nameControl">Must be 8 and 16 characters</small>
      </div>
      <div class="form-group">
        <label>Enter email address</label>
        <input type="email" v-model.lazy="userForm.email" @blur="$v.userForm.email.$touch()">
        <small v-if="emailControl">Invalid email address</small>
        <small v-if="systemControl">This e-mail address is registered in the system.</small>
      </div>
      <div class="form-group">
        <label>Enter your password</label>
        <input type="password" v-model.lazy="userForm.password" @blur="$v.userForm.password.$touch()">
        <small v-if="criteriaControl">At least one uppercase,lowercase,and one digit.</small>
        <small v-if="lengthControl">Password must be between 8-16 characters</small>
      </div>
      <div class="form-group">
        <label>Repassword</label>
        <input type="password" v-model.lazy="userForm.repassword" @blur="$v.userForm.repassword.$touch()">
        <small v-if="rpsControl">passwords do not match</small>
      </div>
      <div class="form-group">
        <label>Enter your age</label>
        <input type="text" v-model.lazy="userForm.age" @blur="$v.userForm.age.$touch()">
        <small v-if="ageControl">Your age should be {{$v.userForm.age.$params.between.min}}-{{$v.userForm.age.$params.between.max}}</small>
      </div>
      <button type="submit" class="btn">Register</button>
  </form>
</template>

<script>
import { required , minLength , maxLength , email , sameAs , numeric ,between } from 'vuelidate/lib/validators'
import axios from '../customAxios'
export default {
  data() {
    return {
      userForm: {
        userName: null,
        email: null,
        password: null,
        repassword: null,
        age: null
      },
    }
  },
   validations: {
    userForm: {
      userName: {
        required,
        minLength: minLength(8),
        maxLength: maxLength(16)   
      },
      email: {
        required,
        email,
        checked: async (val)=>{
          let result = true;
          const Response = await axios.get('emails.json');
          Object.values(Response.data).find((email)=>{
            if(email.mail == val) {
              result = false;
            }
          });
          return result
        }
      },
      password: {
        required,
        minLength: minLength(8),
        maxLength: maxLength(16),
        criteria: (val)=>{
          const containsUppercase = /[A-Z]/.test(val)
          const containsLowercase = /[a-z]/.test(val)
          const containsNumber = /[0-9]/.test(val)
          return containsUppercase && containsLowercase && containsNumber  ? true : false
        }
      },
      repassword: {
        required,
        sameAs: sameAs('password')
      },
      age: {
        required,
        numeric,
        between: between(18,60)
      },
    }
  },
  methods: {
    Submit() {
      !this.$v.$invalid ? alert('Success') : alert('Please review the relevant fields')
    }
  },
  computed: {
    nameControl() {
      return !this.$v.userForm.userName.minLength || !this.$v.userForm.userName.maxLength ? true : false
    },
    emailControl() {
      return !this.$v.userForm.email.email ? true : false 
    },
    systemControl() {
      return !this.$v.userForm.email.checked ? true : false
    },
    criteriaControl() {
      return !this.$v.userForm.password.criteria ? true : false
    },
    lengthControl() {
      return this.$v.userForm.password.minLength && this.$v.userForm.password.maxLength ? false : true
    },
    rpsControl() {
      return !this.$v.userForm.repassword.sameAs && this.$v.userForm.repassword.$model != null || '' ?  true : false 
    },
    ageControl() {
      return !this.$v.userForm.age.between ? true : false
    }
  },
}
</script>

<style lang='scss'>
  @import '@/assets/scss/vueForm.scss';
</style>