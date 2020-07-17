<template>
  <b-container>
    <b-row align-h="center">
      <div class="col-md-4 mt-3"> 
        <b-form>

          <b-form-group label="Your verification code please" label-for="input-1">
            <b-form-input
              id="input-1"
              name="input-1"
              type="number"
              v-model="$v.form.code.$model"
              @keyup="codelength()"
              :state="validateState('code')"
              aria-describedby="input-1-live-feedback"
            ></b-form-input>
            <b-form-invalid-feedback
              id="input-1-live-feedback"
            >This is a required field with 6 digits</b-form-invalid-feedback>
          </b-form-group>

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
import { required, minLength, maxLength } from "vuelidate/lib/validators";
import axios from 'axios'

export default {
  name: 'Register2',
  props: ['id'],
  data() {
    return {
      form: {
        code: '',
      }
    };
  },
  validations: {
    form: {
      code:{
        required,
        minLength: minLength(6),
        maxLength: maxLength(6),
      },
    }
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

      this.submitformdata()
    },

    submitformdata(){
      var myform = {
          'otp': this.form.code
      }
      axios.post('http://test.ok.paymo.uz/public/user/confirm-registration/'+this.id,myform)
      .then((response) => {
        this.$store.commit('SAVEUSER', response.data)
        alert("OK. saved in vuex object -> user")
        this.$router.push({ name: 'Home', query: { redirect: '/' } });
      })
      .catch(function (error) {
        if (error.response) {
          alert('code is wrong')
          console.log(error.response.data);
          console.log(error.response.status);
          console.log(error.response.headers);
        }
      });
      

    },

    codelength(){
      if(this.form.code.length==6)
        this.onSubmit()
    }

  

  },
  
};
</script>

