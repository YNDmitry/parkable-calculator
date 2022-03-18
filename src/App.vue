<template>
  <section class="app-wrapper">
    <div class="calc">
      <div class="calc__main">
        <h1>Parkable ROI calculator</h1>
        <div class="calc__main-actions">
          <div class="calc__slider">
            <div>
              Total number of car parks: 
              <span class="calc__span">{{ firstSlider.currentValue }}</span>
            </div>
            <v-slider
              class="calc__slider-inp"
              v-model="firstSlider.value"
              :tooltips="false"
              :lazy="false"
              :min="firstSlider.min"
              :max="firstSlider.max"
              @update="setFirstSliderValue"
            ></v-slider>
          </div>

          <app-big-slider
            @dropValues="getMultiplesValues"
          ></app-big-slider>

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
            <div>Are you moving to flexible working? </div>

            <div class="calc__radios-wrapper">
              <label 
                for="working-1" 
                class="calc__radio-btn"
                :class="{ 'active': working === '0' ? true : false }"
              >
                <input type="radio" v-model="working" name="working" value="0" id="working-1">
                <span>Yes</span>
              </label>
              <label 
                for="working-2" 
                class="calc__radio-btn"
                :class="{ 'active': working === '1' ? true : false }"
              >
                <input type="radio" v-model="working" name="working" value="1" id="working-2">
                <span>No</span>
              </label>
            </div>
          </div>

          <div class="calc__radios">
            <div>Will you charge staff for parking?</div>

            <div class="calc__radios-wrapper">
              <label 
                for="parking-1" 
                class="calc__radio-btn"
                :class="{ 'active': parking === '0' ? true : false }"
              >
                <input type="radio" v-model="parking" name="parking" value="0" id="parking-1">
                <span>Yes</span>
              </label>
              <label 
                for="parking-2" 
                class="calc__radio-btn"
                :class="{ 'active': parking === '1' ? true : false }"
              >
                <input type="radio" v-model="parking" name="parking" value="1" id="parking-2">
                <span>No</span>
              </label>
            </div>
          </div>

          <transition name="slideDown">
            <div class="calc__slider" v-if="parking === '0'">
              <div>
                Daily Parking rate:
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
          </transition>

          <div class="calc__radios" style="display:none;">
            <div>Country</div>

            <div class="calc__radios-wrapper">
              <label 
                :for="item" 
                class="calc__radio-btn"
                :class="{'active': item === country ? true : false}"
                v-for="item in countries"
                :key="item"
              >
                <input type="radio" v-model="country" :name="item" :value="item" :id="item">
                <span>{{ item }}</span>
              </label>
            </div>
          </div>

          <div class="calc__txt-sm">All ROI figures are estimates based on Parkable subscriber data. </div>
        </div>
      </div>
      <div class="calc__aside">
        <div class="calc__aside-info">
          <div class="calc__aside-info-col">
            <h1>
              <span>Approx.</span><br>
              {{ revenue }} in Revenue 
            </h1>
            <p>
              Annual estimate before cost of transactions fees, 
              sales tax, subscription and software costs.
            </p>
          </div>

          <transition name="slideDown">
            <div class="calc__aside-info-col" v-if="parking === '0'">
              <h1>
                <span>Equivilent to</span><br>
                40 extra parks
              </h1>
              <p>
                Based on better allocation and sharing of under-used parks.
              </p>
            </div>
          </transition>

          <div class="calc__aside-list">
            <div class="calc__aside-list-label">Other benefits:</div>
            <ul>
              <li>Save up to {{ hoursSlider.time }} hours on admin</li>
              <li>Better staff & tenant experiences</li>
              <li>Real-time visibility to fully utilise parks</li>
              <li>Up to 3x more parkers per space</li>
              <li v-if="working === '0'">Better ROI for your flexible workplaces</li>
              <li>Onboarding, training & support</li>
            </ul>
          </div>
        </div>

        <div class="calc__aside-actions">
          <div class="calc__txt-sm">Get more information and a ROI breakdown.</div>
          <button class="btn-primary">
            Get a Breakdown 
            <icon-arrow-right></icon-arrow-right>
          </button>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
  import iconArrowRight from './components/icons/iconArrowRight.vue'
  import VSlider from '@vueform/slider'
  import AppBigSlider from './components/appBigSlider.vue'

  export default {
    components: {
      iconArrowRight,
      VSlider,
      AppBigSlider
    },

    data() {
      return {
        firstSlider: {
          min: 0,
          max: 11,
          step: 1,
          value: 0,
          range: [
            '10', '20', '30', '50', '75', '100', 
            '150', '200', '300', '500', '750', '1000+'
          ],
          currentValue: '10',
        },
        
        rateSlider: {
          min: 2,
          max: 20,
          value: 14,
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

        parking: '0',
        working: '0',
        country: 'AU',
        
        revenue: null,

        TotalBays: null,
        DailyCharge: 5,
        SBCharing: 10,
        VBCharing: 10,
        AgencyFee: 15,
        WDPerYear: 260,
        LSDaysPerYear: 30,
        AWFHDays: 92,
        AWorkDays: 220,
        SalesTax: 10,
        DOFCSession: null,
        DOFUAllocated: null,
        TDOFCSession: null,
        TACPSession: null,
        UOCPSession: 80
      }
    },

    mounted() {
      this.setRevenue()
    },
    
    updated() {
      this.setDOFCSession
      this.setDOFUAllocated
      this.setTDOFCSession
      this.setTACPSession
      
      this.setRevenue()
    },

    watch: {
      calkNumbers(val) {
        console.log(val)
      }
    },

    computed: {
      setDOFCSession() {
        const formula = this.TotalBays[1] * this.WDPerYear
        
        return this.DOFCSession = Math.ceil(formula)
      },
      
      setDOFUAllocated() {
        const formula = this.TotalBays[2] * (this.AWFHDays + this.LSDaysPerYear)
        return this.DOFUAllocated = Math.ceil(formula)
      },

      setTDOFCSession() {
        const formula = this.setDOFUAllocated + this.setDOFCSession
        return this.TDOFCSession = Math.floor(formula)
      },

      setTACPSession() {
        return this.TACPSession = this.setTDOFCSession
      },
    },

    watch: {
      working(val) {
        if (val === '0') {
          this.AWFHDays = 92
        } else {
          this.AWFHDays = 10
        }
        this.setDOFUAllocated
      }
    },

    methods: {
      getMultiplesValues(data) {
        return this.TotalBays = data
      },

      setFirstSliderValue(value) {
        this.firstSlider.currentValue = this.firstSlider.range[value]
      },

      setHours(value) {
        this.hoursSlider.time = Math.ceil(value * 52 * 0.9)
      },

      setRevenue() {
        const formula = (this.UOCPSession / 100) * this.TACPSession * this.DailyCharge

        console.log(this.UOCPSession / 100)
        return this.revenue = formula
      },
    }
  }
</script>

<style src="@vueform/slider/themes/default.css"></style>