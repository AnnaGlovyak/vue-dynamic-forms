<template>
  <div>
    <div v-if="wizarsInProcess" v-show="asyncState !== 'pending'">
      <keep-alive>
        <component
          :is="currentStep"
          ref="currentStepRef"
          :wizarData="form"
          @updateAsyncState="updateAsyncState"
        />
      </keep-alive>

      <div class="progress-bar">
        <div :style="`width: ${progress}%;`"></div>
      </div>

      <!-- Actions -->
      <div class="buttons">
        <button
          @click="goBack"
          v-if="currentStepNumber > 1"
          class="btn-outlined"
        >Back
        </button>
        <button
          @click="nextButtonAction"
          class="btn"
        >{{ isLastStep ? 'Complete order' : 'Next' }}</button>
      </div>

      <pre><code>{{form}}</code></pre>
    </div>
    <div v-else>
      <h1 class="title">Thank you!</h1>
      <h2 class="subtitle">
        We look forward to shipping you your first box!
      </h2>
    </div>

    <div class="loading-wrapper" v-if="asyncState === 'pending'">
       <div class="loader">
         <img src="/spinner.svg" alt="">
         <p>Please wait, we're hitting our servers!</p>
       </div>
     </div>
  </div>
</template>

<script>
import FormPlanPicker from './FormPlanPicker.vue'
import FormUserDetails from './FormUserDetails.vue'
import FormAddress from './FormAddress.vue'
import FormReviewOrder from './FormReviewOrder.vue'
// api
import {postFormToDB} from "../api"
export default {
  name: 'FormWizard',
  components: {
    FormPlanPicker,
    FormUserDetails,
    FormAddress,
    FormReviewOrder
  },
  data () {
    return {
      currentStepNumber: 1,
      asyncState: null,
      form: {
        plan: null,
        email: null,
        name: null,
        password: null,
        address: null,
        recipient: null,
        chocolate: false,
        otherTreat: false
      },
      steps: [
        'FormPlanPicker',
        'FormUserDetails',
        'FormAddress',
        'FormReviewOrder'
      ]
    }
  },
  computed: {
    length () {
      return this.steps.length
    },
    currentStep () {
      return this.steps[this.currentStepNumber - 1]
    },
    progress () {
      return this.currentStepNumber/this.length * 100
    },
    isLastStep () {
      return this.currentStepNumber === this.length
    },
    wizarsInProcess () {
      return this.currentStepNumber <= this.length
    }
  },
  methods: {
    goBack () {
      this.currentStepNumber--
    },
    goNext () {
      this.currentStepNumber++;
    },
    nextButtonAction () {
      this.$refs.currentStepRef.submit()
        .then(stepData => {
          Object.assign(this.form, stepData);
          if (this.isLastStep) {
            this.submitOrder()
          }
          else {
            this.goNext()
          }
        })
        .catch(error => console.log(error))
    },
    submitOrder () {
      this.asyncState = 'pending';
      postFormToDB(this.form)
        .then(() => {
          console.log("Order submited");
          this.asyncState = 'success';
          this.currentStepNumber++;
        })
    },
    updateAsyncState (state) {
      this.asyncState = state;
    }
  }
}
</script>
