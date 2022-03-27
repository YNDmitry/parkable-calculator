<template>
  <section class="app-wrapper">
    <div class="calc">
      <app-tab-1 
        @revenue="setRevenue"
        @workingVal="setWorkingVal"
        @parkingVal="setParkingVal"
        @EAParks="setEAParks"
        @time="setTime"
        @formData="setFormData"
        :tab="tab"
        v-show="tab === 1"
      ></app-tab-1>
      <app-tab-2 
        @updateTab="changeTab"
        v-show="tab === 2"
      ></app-tab-2>

      <div class="calc__aside">
        <app-aside-tab-1
          :revenue="revenue"
          :working="working"
          :parking="parking"
          :EAParks="EAParks"
          :time="time"
          @updateTab="changeTab"
          v-if="tab === 1"
        ></app-aside-tab-1>
        <app-aside-tab-2
          :time="time"
          v-else
        ></app-aside-tab-2>
      </div>
    </div>
  </section>
</template>

<script>
  import AppAsideTab1 from './components/AppAside/AppAsideTab1.vue'
  import AppAsideTab2 from './components/AppAside/AppAsideTab2.vue'
  import AppTab1 from './components/AppTabs/AppTabsTab1.vue'
  import AppTab2 from './components/AppTabs/AppTabsTab2.vue'

  export default {
    components: {
      AppAsideTab1,
      AppAsideTab2,
      AppTab1,
      AppTab2,
    },

    data() {
      return {
        tab: 1,
        revenue: null,
        EAParks: null,
        working: '1',
        parking: '1',
        time: null,
      }
    },

    methods: {
      setRevenue(val) {
        return this.revenue = val
      },

      setWorkingVal(val) {
        return this.working = val
      },

      setParkingVal(val) {
        return this.parking = val
      },

      setEAParks(val) {
        this.EAParks = val
      },

      setTime(val) {
        this.time = val
      },

      changeTab(currentTab) {
        const appTop = document.querySelector('#app').offsetTop
        this.tab = currentTab
        return window.scrollTo(0, appTop)
      },

      setFormData(data) {
        sessionStorage.setItem('formData', JSON.stringify(data))
      }
    }
  }
</script>
