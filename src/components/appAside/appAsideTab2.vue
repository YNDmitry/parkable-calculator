<template>
  <div class="calc__aside-col2">
    <div class="calc__aside-head">
      <icon-logo></icon-logo>
      <p>Unlock a smarter, user-friendly<br>platform for managing parking.</p>
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
      <img class="calc__aside-list-cars" sizes="(max-width: 479px) 100vw, 420px" srcset="https://assets.website-files.com/618d84f7039384e134b466ec/62386d8db3ac6345b7cb4d9e_p-cars%20(1)-p-500.png 500w, https://assets.website-files.com/618d84f7039384e134b466ec/62386d8db3ac6345b7cb4d9e_p-cars%20(1).png 788w" alt="" src="https://assets.website-files.com/618d84f7039384e134b466ec/62386d8db3ac6345b7cb4d9e_p-cars%20(1).png">
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