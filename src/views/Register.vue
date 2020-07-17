<template>
  <b-container>
    <b-row align-h="center">
      <div class="col-md-4"> 
        <b-form @submit.stop.prevent="onSubmit">

          <b-form-group label="Fullname" label-for="input-1">
            <b-form-input
              id="input-1"
              name="input-1"
              v-model="$v.form.fullname.$model"
              :state="validateState('fullname')"
              aria-describedby="input-1-live-feedback"
            ></b-form-input>
            <b-form-invalid-feedback
              id="input-1-live-feedback"
            >This is a required field.</b-form-invalid-feedback>
          </b-form-group>

          <b-form-group label="Nickname" label-for="input-2">
            <b-form-input
              id="input-2"
              name="input-2"
              v-model="$v.form.nickname.$model"
              :state="validateState('nickname')"
              aria-describedby="input-2-live-feedback"
            ></b-form-input>

            <b-form-invalid-feedback
              id="input-2-live-feedback"
            >This is a required field and must be at least 5 characters. Only alphanumeric and must contain at least one of "-", "_", "."</b-form-invalid-feedback>
          </b-form-group>


          <b-form-group label="Password" label-for="input-3">
            <b-form-input
              id="input-3"
              name="input-3"
              type="password"
              v-model="$v.form.password.$model"
              :state="validateState('password')"
              aria-describedby="input-3-live-feedback"
            ></b-form-input>

            <b-form-invalid-feedback
              id="input-3-live-feedback"
            >This is a required field and must be at least 8 characters. Only alphanumeric and must contain at least one of "-", "_", ".", "+", "=", "@", "$", "!", "?"</b-form-invalid-feedback>
          </b-form-group>

          <b-form-group label="Repeat password" label-for="input-4">
            <b-form-input
              id="input-4"
              name="input-4"
              type="password"
              v-model="$v.form.repeatpassword.$model"
              :state="validateState('repeatpassword')"
              aria-describedby="input-4-live-feedback"
            ></b-form-input>

            <b-form-invalid-feedback
              id="input-4-live-feedback"
            >Make sure this field matches your password</b-form-invalid-feedback>
          </b-form-group>

          <b-form-group label="Phone" label-for="input-5">
            <b-form-input
              id="input-5"
              name="input-5"
              v-model="$v.form.phone.$model"
              :state="validateState('phone')"
              aria-describedby="input-5-live-feedback"
            ></b-form-input>

            <b-form-invalid-feedback
              id="input-5-live-feedback"
            >This is a required field and must be 12 numbers.</b-form-invalid-feedback>
          </b-form-group>

          <b-form-group label="Gender" label-for="input-6">
            <b-form-select
              id="input-6"
              name="input-6"
              v-model="genderselection"
              :options="gender"
            ></b-form-select>


          </b-form-group>

          <b-form-group label="Country" label-for="input-7">
            <b-form-select
              id="input-7"
              name="input-7"
              v-model="countryselection"
            >
              <b-form-select-option v-for="country in countries" :key="country.name" :value="country.name">{{country.name}}</b-form-select-option>
            </b-form-select>

          </b-form-group>

          <b-form-group label="City (only capital for now)" label-for="input-8">
            <b-form-select
              id="input-8"
              name="input-8"
              v-model="cityselection"
              :disabled="countryselection==''"
            >
              <b-form-select-option :value="getcity" >{{getcity}}</b-form-select-option>
            </b-form-select>

          </b-form-group>

          <b-button type="submit" variant="primary" :disabled="buttondisabled">Continue</b-button>
        </b-form>
      </div>
    </b-row>     
  </b-container>

</template>


<style>
body {
  padding: 1rem;
}
</style>

<script>
import { required, minLength, maxLength, helpers, sameAs } from "vuelidate/lib/validators";
import axios from 'axios'


const nicknamevalidation = helpers.regex('nicknamevalidation', /^(?=.*[A-Za-z\d])(?=.*(-|_|\.))[A-Za-z\d-_.]{5,}$/)
const passwordvalidation = helpers.regex('passwordvalidation', /^(?=.*[A-Za-z\d])(?=.*(-|_|\.|\+|=|@|\$|!|\?))[A-Za-z\d-_.+=@$!?]{8,}$/)
const phonevalidation = helpers.regex('phonevalidation', /^[\d]*$/)


export default {
  name: 'Register',
  data() {
    return {
      gender: [
        { value: "male", text: "Male" },
        { value: "female", text: "Female" }
      ],
      genderselection: null,
      countriescapitals: [],
      countries: [], 
      countryselection: '', 
      city:[],
      cityselection: '', 
      form: {
        fullname: '',
        nickname: '',
        password: '',
        repeatpassword: '',
        phone: '998',
      }
    };
  },
  validations: {
    form: {
      fullname:{
        required,
      },
      nickname: {
        required,
        nicknamevalidation,
      },
      password: {
        required,
        passwordvalidation
      },
      repeatpassword:{
        sameAsPassword: sameAs('password')
      },
      phone: {
        required,
        minLength: minLength(12),
        maxLength: maxLength(12),
        phonevalidation
      },
      gender: {

      }


    }
  },
  mounted() {
    this.getcountires()
  },  
  methods: {
    validateState(name) {
      const { $dirty, $error } = this.$v.form[name];
      return $dirty ? !$error : null;
    },
    onSubmit() {
      this.$v.form.$touch();
      if (this.$v.form.$anyError) {
        return;
      }

      alert("It is ok now, redirecting to step2!")
      this.submitformdata()
    },

    getcountires(){
      axios.get('https://restcountries.eu/rest/v2/all?fields=name;capital').then((response) => {
        this.countriescapitals = response.data
        this.countries = response.data.filter(country => country.name)
      })
    },


    submitformdata(){
      var myform = {
          'fullName': this.form.fullname,
          'nickname': this.form.nickname,
          'password': this.form.password,
          'repeatPassword': this.form.repeatpassword,
          'phone': this.form.phone,
          'gender': this.genderselection,
          'country': this.countryselection,
          'city': this.cityselection,
      }
      console.log(myform)
      axios.post('http://test.ok.paymo.uz/public/user/register',myform)
      .then((response) => {
        this.$router.push({ name: 'Register2', params: { id: response.data.registrationId }, query: { redirect: '/register2' } });
      })
      .catch(function (error) {
        if (error.response) {
          console.log(error.response.data);
          console.log(error.response.status);
          console.log(error.response.headers);
        }
      });
      

    }

  },
  computed: {
    getcity(){
      if ( this.countryselection )
        return this.countriescapitals.filter(country=> country.name.includes(this.countryselection) )[0].capital
      else 
        return 0
    },
    buttondisabled(){
      return !( this.validateState('fullname') && this.validateState('nickname') && this.validateState('password') && this.validateState('repeatpassword') && this.validateState('phone') )
    }
    
  }
};
</script>

