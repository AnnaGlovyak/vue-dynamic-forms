<template>
  <div>
    <h1 class="title">Confirm your order</h1>

    <h2 class="subtitle">
      We're almost there!
    </h2>

    <div class="summary">
      <h3>Subscription</h3>

      <p class="description">
        We'll send you carefully selected coffee every month.
      </p>

      <div class="plans">
        <div class="plan active-plan">
          <div class="weight">
            {{ wizarData.plan.weight }}
          </div>

          <div class="description">
            <span class="title">
              {{ wizarData.plan.name }}
            </span>
            <span class="description">
              {{ wizarData.plan.description }}
            </span>
          </div>

          <div class="price">
            <span class="dollar-sign">$</span>
            <span class="number">{{totalPrice}}</span>
          </div>
        </div>
      </div>

      <h3>
        Level up your box
      </h3>

      <p class="description">
        Treat yourself by leveling up your monthly box
      </p>

      <div class="options">
        <div class="option">
          <input v-model="form.chocolate" type="checkbox" value="chocolate" id="chocolate">
          <label for="chocolate">4 pcs. Single Origin Chocolate (+$4/month)</label>
        </div>

        <div class="option">
          <input v-model="form.otherTreat" type="checkbox" value="chocolate" id="other_treat">
          <label for="other_treat">Another delicious treat (+$2/month)</label>
        </div>
      </div>

      <div class="address">
        <div class="w-2/3">
          <h3>Delivery</h3>
          <p class="description">
            Your first Liquid Gold Box is right around the corner
          </p>
        </div>

        <div class="w-1/3">
          <h3>{{ wizarData.recipient }}</h3>
          <p class="leading-normal">
            {{ wizarData.address }}
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import { useVuelidate } from '@vuelidate/core'
  export default {
    props: {
      wizarData: {
        type: Object,
        required: true,
      }
    },
    setup () {
      return { v$: useVuelidate() }
    },
    data () {
      return {
        form: {
          chocolate: false,
          otherTreat: false
        }
      }
    },
    validations () {
      return {}
    },
    computed: {
      totalPrice () {
        let total = this.wizarData.plan.price
        console.log(total);
        if (this.form.chocolate) {
          total +=4
        }
        if (this.form.otherTreat) {
          total +=2
        }
        return total
      }
    },
    methods: {
      submit () {
        return Promise.resolve({
          chocolate: this.form.chocolate,
          otherTreat: this.form.otherTreat,
        });
      }
    }
  }
</script>

<style scoped>

</style>