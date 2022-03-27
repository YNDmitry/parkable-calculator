<template>
  <div class="calc__main">
    <button class="calc__main-back" @click="changeTab(1)">
      <icon-back></icon-back>
      Back to ROI calculator
    </button>
    <h1>Get a breakdown & more information</h1>
    <div class="calc__main-form"></div>
  </div>
</template>

<script>
  import iconBack from '../icons/iconBack.vue'
  export default {
    components: { iconBack },
    methods: {
      changeTab(value) {
        this.$emit('updateTab', value)
      }
    }
  }
</script>