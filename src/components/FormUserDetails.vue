<template>
  <div>
    <h1 class="title">Create Account</h1>

    <h2 class="subtitle">
      Create an account or log in to order your liquid gold subscription
    </h2>

    <form
      v-if="!loggedIn"
      class="form"
    >
      <div class="form-group">
        <label class="form-label" for="email">Email</label>
        <input
          type="text"
          v-model="v$.form.email.$model"
          placeholder="your@email.com"
          class="form-control"
          id="email"
          :disabled="emailCheckedInDB"
        />
        <div v-if="emailCheckedInDB" class="error info">
          <a href="#" @click="reset">Not you?</a>
        </div>
        <!-- <div v-if="v$.form.email.$error" class="error">email is required</div> -->
        <div v-if="v$.form.email.$error" class="error">email is invalid</div>
      </div>


      <div v-if="emailCheckedInDB" class="form-group">
        <label class="form-label" for="password">Password</label>
        <input v-model="v$.form.password.$model" type="password" placeholder="Super Secret Password" class="form-control" id="password">
        <div v-if="v$.form.password.$error" class="error">password is required</div>
        <!-- <div v-if="v$.form.password.$error && !v$.form.password.correct" class="error">password is invalid - try again</div> -->
      </div>

      <div v-if="!existingUser && emailCheckedInDB" class="form-group">
        <label class="form-label" for="name">Name</label>
        <input v-model="v$.form.name.$model" type="text" placeholder="What should we call you?" class="form-control" id="name">
        <div v-if="v$.form.name.$error" class="error">name is required</div>
      </div>
    </form>
    <div v-else class="text-center">
      Succesfully logged in! <a href="#" @click="reset"> Not {{ form.name }}?</a>
    </div>
  </div>
</template>

<script>
  import { useVuelidate } from '@vuelidate/core'
  import {required, email} from '@vuelidate/validators'
  // api
  import {authenticateUser, checkIfUserExistsInDB} from '../api'
  export default {
    setup () {
      return { v$: useVuelidate() }
    },
    data () {
      return {
        form: {
          email: null,
          password: null,
          name: null,
        },
        emailCheckedInDB: false,
        existingUser: false,
        wrongPassword: false,
      }
    },
    validations () {
      return {
        form: {
          email: {
            required,
            email
          },
          password: {
            required,
            correct () {
              return !this.wrongPassword;
            }
          },
          name: {
            required
          }
        }
      }
    },
    computed: {
      loggedIn () {
        return this.existingUser && this.form.name;
      }
    },
    methods: {
      submit () {
        let job;
        if (!this.emailCheckedInDB) {
          this.v$.form.email.$touch();
          job = this.checkIfUserExist();
        }
        else {
          console.log(this.existingUser && !this.loggedIn);
          if (this.existingUser && !this.loggedIn) {
            this.v$.form.password.$touch();
            job = this.login();
          }
          else {
            this.v$.$touch();
            job = Promise.resolve();
          }
        }

        return new Promise((resolve, reject) => {
          job.then(() => {
            if (!this.v$.$invalid) {
              resolve({
                email: this.form.email,
                password: this.form.password,
                name: this.form.name
              });
            }
            else {
              reject('data is not valid yet');
            }
          })
          .catch(error => reject(error));
        });
      },
      checkIfUserExist () {
        if (!this.v$.form.email.$invalid) {
          this.$emit('updateAsyncState', 'pending');
          return checkIfUserExistsInDB(this.form.email)
            .then(() => {
              this.existingUser = true;
              this.emailCheckedInDB = true;
              this.$emit('updateAsyncState', 'success');
            })
            .catch(() => {
              this.existingUser = false;
              this.emailCheckedInDB = true;
              this.$emit('updateAsyncState', 'success');
            })
        }
        else {
          return Promise.reject('email is invalid');
        }
      },
      login () {
        this.wrongPassword = false;
        if (!this.v$.form.password.$invalid) {
          this.$emit('updateAsyncState', 'pending');
          return authenticateUser(this.form.email, this.form.password)
            .then(user => {
              this.form.name = user.name;
              this.$emit('updateAsyncState', 'success');
            })
            .catch(() => {
              this.wrongPassword = true;
              this.$emit('updateAsyncState', 'success');
            })
        }
        else {
          return Promise.reject('password is invalid');
        }
      },
      reset () {
        this.form.email = null;
        this.form.password = null;
        this.form.name = null;
        this.emailCheckedInDB = false;
        this.existingUser = false;
        this.wrongPassword = false;
        this.v$.$reset();
      }
    }
  }
</script>

<style scoped>

</style>