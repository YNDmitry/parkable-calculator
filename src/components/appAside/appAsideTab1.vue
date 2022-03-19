<template>
  <div class="calc__aside-col1">
    <div class="calc__aside-info">
      <div class="calc__aside-info-col">
        <h1>
          <span>Generate up to*</span><br>
          {{ convertToUSD(revenue) }} in Revenue 
        </h1>
        <p>
          Annual estimate before cost of transactions fees, 
          sales tax, subscription and software costs.
        </p>
      </div>

      <div class="calc__aside-info-col" v-if="parking === '0'">
        <h1>
          <span>Equivalent to*</span><br>
          {{ EAParks }} extra parks
        </h1>
        <p>
          Based on better allocation and sharing of under-used parks.
        </p>
      </div>

      <div class="calc__aside-list">
        <div class="calc__aside-list-label">Other benefits:</div>
        <ul>
          <li>Save up to {{ time }} hours on admin*</li>
          <li>Better staff & tenant experiences</li>
          <li>Real-time visibility to fully utilise parks</li>
          <li>Easy booking and sharing</li>
          <li v-if="working === '0'">Better ROI for your flexible workplaces</li>
          <li>Onboarding, training & support</li>
        </ul>
      </div>
    </div>

    <div class="calc__aside-actions">
      <div class="calc__txt-sm">Get more information and a ROI breakdown.</div>
      <button class="btn-primary" @click="changeTab(2)">
        Get a Breakdown 
        <icon-arrow-right></icon-arrow-right>
      </button>
    </div>
  </div>
</template>

<script>
  import iconArrowRight from '../icons/iconArrowRight.vue'

  export default {
    components: { iconArrowRight },

    props: { 
      working: {
        type: String
      },
      parking: {
        type: String
      },
      EAParks: {
        type: Number
      },
      revenue: {
        type: Number
      },
      time: {
        type: Number
      }
    },

    emits: ['updateTab'],

    methods: {
      convertToUSD(someNumber) {
        return new Intl.NumberFormat('en-US', {
          style: 'currency',
          currency: 'USD',
        }).format(someNumber)
      },

      changeTab(tab) {
        this.$emit('updateTab', tab)
      }
    }
  }
</script>