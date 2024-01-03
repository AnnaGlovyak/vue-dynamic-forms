<template>
  <div>
    <div v-if="wizarsInProcess">
      <keep-alive>
        <component
          :is="currentStep"
          ref="currentStepRef"
          :wizarData="form"
          @update="processStep"
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
          :disabled="!canGoNext"
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
  </div>
</template>

<script>
import FormPlanPicker from './FormPlanPicker.vue'
import FormUserDetails from './FormUserDetails.vue'
import FormAddress from './FormAddress.vue'
import FormReviewOrder from './FormReviewOrder.vue'
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
      canGoNext: false,
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
    processStep (step) {
      console.log("HAVE STAP DATA FROM EMIT", step);
      Object.assign(this.form, step.data)
      this.canGoNext = step.valid
      console.log(this.$refs.currentStepRef);
    },
    goBack () {
      this.currentStepNumber--
      this.canGoNext = true
    },
    goNext () {
      this.currentStepNumber++
      this.$nextTick(() => {
        this.canGoNext = !this.$refs.currentStepRef.v$.$invalid
        // or calll submit method 
        // this.$refs.currentStepRef.submit()
      })
    },
    nextButtonAction () {
      if (this.isLastStep) {
        this.submitOrder()
      }
      else {
        this.goNext()
      }
    },
    submitOrder () {
      // do ajax request
      console.log("Order submited")
      this.currentStepNumber++
    }
  }
}
</script>
