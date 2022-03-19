<template>
  <div class="calc__aside-col2">
    <div class="calc__aside-head">
      <icon-logo></icon-logo>
      <p>Unlock a smarter, user-friendly platform for managing parking.</p>
    </div>
    <div class="calc__aside-info">
      <div class="calc__aside-info-col">
        <div class="calc__aside-list-label">Parkable benefits:</div>
        <ul>
          <li>Save up to {{ time }} hours on admin*</li>
          <li>Up to 3x more parkers per space*</li>
          <li>Better staff & tenant experiences</li>
          <li>Integrated hardware</li>
          <li>Unlock new revenue</li>
        </ul>
      </div>
      <img class="calc__aside-list-cars" src="https://uploads-ssl.webflow.com/618d84f7039384e134b466ec/62360548e9fbf8958458de6c_calc-cars.png" alt="">
    </div>

  </div>
</template>

<script>
  import iconLogo from '../icons/iconLogo.vue'
  export default {
    components: { iconLogo },
    props: {
      time: {
        type: Number
      }
    }
  
  }
</script>