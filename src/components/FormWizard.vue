<template>
  <div>
    <FormPlanPicker v-if="currentStepNumber === 1" @update="processStep"/>
    <FormUserDetails v-if="currentStepNumber === 2" @update="processStep"/>
    <FormAddress v-if="currentStepNumber === 3" @update="processStep" :wizarData="form"/>
    <FormReviewOrder v-if="currentStepNumber === 4" @update="processStep" :wizarData="form"/>

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
        @click="goNext"
        :disabled="!canGoNext"
        class="btn"
      >Next</button>
    </div>

    <pre><code>{{form}}</code></pre>
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
      length: 4,
      form: {
        plan: null,
        email: null,
        name: null,
        password: null,
        address: null,
        recipient: null,
        chocolate: false,
        otherTreat: false
      }
    }
  },
  computed: {
    progress () {
      return this.currentStepNumber/this.length * 100
    }
  },
  methods: {
    processStep (stepData) {
      console.log("HAVE STAP DATA FROM EMIT", stepData);
      Object.assign(this.form, stepData)
      this.canGoNext = true;
    },
    goBack () {
      this.currentStepNumber--
    },
    goNext () {
      this.currentStepNumber++
      this.canGoNext = false
    }
  }
}
</script>
