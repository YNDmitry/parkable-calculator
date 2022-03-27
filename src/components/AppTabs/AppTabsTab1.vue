<template>
  <div class="calc__main">
    <h1>Parkable ROI calculator</h1>

    <div class="calc__main-actions">

      <app-big-slider
        @tBays="getTBays"
        @dropValues="getCalcValues"
      ></app-big-slider>
      <input type="text" v-model="TotalBays" style="display: none;">

      <div class="calc__slider">
        <div>
          Parking admin hours/week:
          <span class="calc__span">{{ hoursSlider.value }}h</span>
        </div>
        <v-slider
          class="calc__slider-inp"
          v-model="hoursSlider.value"
          :tooltips="false"
          :lazy="false"
          :min="hoursSlider.min"
          :max="hoursSlider.max"
          :step="2"
          @update="setHours"
        ></v-slider>
      </div>

      <div class="calc__radios">
        <div>Do you have a hybrid workplace?</div>
        <div class="calc__radios-wrapper" @click="toggleWorks(working)">
          <label 
            for="working-1" 
            class="calc__radio-btn"
            :class="{ 'active': working === '1' ? true : false }"
          >
            <input type="radio" v-model="working" name="working" value="1" id="working-1">
            <span>Yes</span>
          </label>
          <label 
            for="working-2" 
            class="calc__radio-btn"
            :class="{ 'active': working === '0' ? true : false }"
          >
            <input type="radio" v-model="working" name="working" value="0" id="working-2">
            <span>No</span>
          </label>
        </div>
      </div>

      <div class="calc__radios">
        <div>Will you charge for parking?</div>

        <div class="calc__radios-wrapper" @click="toggleParking(parking)">
          <label 
            for="parking-1" 
            class="calc__radio-btn"
            :class="{ 'active': parking === '1' ? true : false }"
          >
            <input type="radio" v-model="parking" name="parking" value="1" id="parking-1">
            <span>Yes</span>
          </label>
          <label
            for="parking-2" 
            class="calc__radio-btn"
            :class="{ 'active': parking === '0' ? true : false }"
          >
            <input type="radio" v-model="parking" name="parking" value="0" id="parking-2">
            <span>No</span>
          </label>
        </div>
      </div>

      <div class="calc__slider" v-if="parking === '1'">
        <div>
          Daily parking rate:
          <span class="calc__span">${{ rateSlider.value }} <span>per day</span></span>
        </div>
        <v-slider
          class="calc__slider-inp"
          v-model="rateSlider.value"
          :tooltips="false"
          :lazy="false"
          :min="rateSlider.min"
          :max="rateSlider.max"
          :step="2"
        ></v-slider>
      </div>

      <div class="calc__radios">
        <div>Country</div>

        <div class="calc__radios-wrapper wrapper--countries">
          <label 
            :for="item" 
            class="calc__radio-btn"
            :class="{'active': item === country ? true : false}"
            v-for="item in countries"
            :key="item"
          >
            <input type="radio" 
              @change="changeCountry" 
              v-model="country" 
              :name="item" 
              :value="item" 
              :id="item"
            >
            <span>{{ item }}</span>
          </label>
        </div>
      </div>

      <div class="calc__txt-sm">*All ROI figures are estimates based on Parkable subscriber data. Conditions apply.</div>
    </div>
  </div>
</template>

<script>
  import VSlider from '@vueform/slider'
  import iconArrowRight from '../icons/iconArrowRight.vue'
  import AppBigSlider from '../AppBigSlider.vue'

  export default {
    components: {
      VSlider,
      iconArrowRight,
      AppBigSlider,
    },

    props: {
      tab: {
        type: Number
      }
    },

    data() {
      return {
        sliderTBays: null,
        
        rateSlider: {
          min: 2,
          max: 20,
          value: 8,
        },
        
        hoursSlider: {
          min: 2,
          max: 20,
          value: 6,
          time: null
        },

        countries: [
          'USA', 'UK', 'AU', 'NZ'
        ],

        parking: '1',
        working: '1',
        country: 'AU',

        TotalBays: null,
        SBCharing: 10,
        VBCharing: 10,
        AgencyFee: 15,
        WDPerYear: 260,
        LSDaysPerYear: 30,
        AWFHDays: 92,
        AWorksDays: null,
        SalesTax: 10,
        DOFCSession: null,
        DOFUAllocated: null,
        TDOFCSession: null,
        TACPSession: null,
        UOCPSession: 76,

        DYEAPIUnused: null,
        APUPercentage: null,
        EAAParks: null,
        EAParks: null,
        TCIncrease: null
      }
    },

    emits: ['revenue', 'workingVal', 'parkingVal', 'EAParks', 'time', 'formData'],

    mounted() {
      this.setRevenue()
      this.setHours(this.hoursSlider.value)
      this.getTBays()
      this.setEAAParks
    },

    updated() {
      this.setEAAParks
      this.setDYEAPIUnused
      this.setTCIncrease

      if (this.rateSlider.value) {
        this.setRevenue()
      }

      this.$emit('formData', {
        'Total Bays': this.sliderTBays === undefined ? '10' : this.sliderTBays,
        'Parks': this.TotalBays,
        'Parking admin hours/week': this.hoursSlider.time,
        'Flexible workplace': this.workplace === '1' ? 'Yes' : 'No',
        'Charge for parking?': this.parking === '1' ? 'Yes' : 'No',
        'Daily parking rate': '$' + this.rateSlider.value + ' per day',
        'Country': this.country
      })
    },
    
    computed: {
      setAWorksDays() {
        return this.AWorksDays = this.WDPerYear - this.LSDaysPerYear - this.AWFHDays
      },

      setDOFCSession() {
        const formula = this.TotalBays[1] * this.WDPerYear
        return this.DOFCSession = Math.round(formula)
      },
      
      setDOFUAllocated() {
        const formula = this.TotalBays[2] * (this.AWFHDays + this.LSDaysPerYear)
        return this.DOFUAllocated = Math.round(formula)
      },

      setTDOFCSession() {
        const formula = this.setDOFUAllocated + this.setDOFCSession
        return this.TDOFCSession = Math.round(formula)
      },

      setTACPSession() {
        return this.TACPSession = this.setTDOFCSession
      },

      setDYEAPIUnused() {
        return this.DYEAPIUnused = this.WDPerYear - this.setAWorksDays
      },

      setAPUPercentage() {
        return this.APUPercentage = ((this.setDYEAPIUnused / this.WDPerYear) * 100).toFixed(1)
      },

      setEAAParks() {
        const result = ((this.setAPUPercentage * this.TotalBays[2]) / 100).toFixed(1)
        this.EAParks = result
        this.EAAParks = result

        return this.$emit('EAParks', result)
      },

      setTCIncrease() {
        return this.TCIncrease = 
          ((this.EAParks / this.sliderTBays) * 100).toFixed(1)
      },
    },

    watch: {
      working(val) {
        if (val === '1') {
          this.AWFHDays = 92
        } else {
          this.AWFHDays = 10
        }

        this.setRevenue()
        return this.$emit('workingVal', val)
      },

      parking(val) {
        if (val === '1') {
          this.rateSlider.value = 14
        } else {
          this.rateSlider.value = 0
        }

        this.setRevenue()
        return this.$emit('parkingVal', val)
      },
    },

    methods: {
      getTBays(data) {
        this.sliderTBays = data
        return this.setRevenue()
      },

      getCalcValues(data) {
        this.TotalBays = data
        return this.setRevenue()
      },

      setHours(value) {
        this.hoursSlider.time = Math.ceil(value * 52 * 0.9)
        return this.$emit('time', this.hoursSlider.time)
      },

      setRevenue() {
        let value
        if (this.rateSlider.value !== 0) {
          value = this.setTDOFCSession * this.rateSlider.value * (this.UOCPSession / 100)
        } else {
          value = this.setTDOFCSession * (this.UOCPSession / 100)
        }

        this.revenue = value

        return this.$emit('revenue', value)
      },

      changeCountry() {
        if (this.country === 'UK') {
          this.SBCharing = 7
          this.VBCharing = 10
        } else if (this.country === 'USA' || 'AU' || 'NZ') {
          this.SBCharing = 10
          this.VBCharing = 15
        }

        if (this.country === 'UK') {
          this.LSDaysPerYear = 32.4
        } else if (this.country === 'USA') {
          this.LSDaysPerYear = 15
        } else if (this.country === 'NZ' || 'AU') {
          this.LSDaysPerYear = 30 
        }

        if (this.country === 'UK') {
          this.SalesTax = 20
        } else if (this.country === 'USA') {
          this.SalesTax = 7.50
        } else if (this.country === 'NZ') {
          this.SalesTax = 15
        } else if (this.country === 'AU') {
          this.SalesTax = 10
        }

        this.setRevenue()
      },

      toggleWorks(value) {
        return value === '1' ? this.working = '0' : this.working = '1'
      },

      toggleParking(value) {
        return value === '1' ? this.parking = '0' : this.parking = '1'
      }
    }
  }
</script>

<style src="@vueform/slider/themes/default.css"></style>