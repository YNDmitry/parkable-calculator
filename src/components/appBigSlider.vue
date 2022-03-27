<template>
  <div>
    <div class="calc__slider">
      <div>
        Total number of car parks: 
        <span class="calc__span">
          {{ currentValue }}{{ currentValue === '1000' ? '+' : '' }}
        </span>
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
    <div class="calc__big-slider-wrapper">
      <div class="calc__big-slider-head">
        <div v-for="tag in tags" :key="tag">{{ tag.name }}</div>
      </div>
      <div class="calc__big-slider" ref="bigSlider">
        <div
          class="calc__big-slider-tag"
          ref="slider"
          v-for="tag, index in tags"
          :key="index"
          :data-tag-index="index"
          :style="{
            width: tag.value + '%'
          }"
        >
          <span>{{ tag.value }}%</span>
          <div class="slider-handle" 
            :data-tag="tag.name" 
            @mousedown.prevent="startDrag" 
            @mousemove.prevent="doDrag"
            @mouseup.prevent="stopDrag"
            @touchstart.prevent="startDrag"
            @touchend.prevent="stopDrag"
            @touchcancel.prevent="stopDrag"
            @touchmove.prevent="doDrag"
            ref="handle"
          >
            <icon-resize-arrow class="slider-handle__icon"></icon-resize-arrow>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import VSlider from '@vueform/slider'
import iconResizeArrow from './icons/iconResizeArrow.vue'
export default {
  components: { 
    VSlider,
    iconResizeArrow
  },

  data() {
    return {
      firstSlider: {
        min: 0,
        max: 11,
        step: 1,
        value: 5,
        range: [
          '10', '20', '30', '50', '75', '100', 
          '150', '200', '300', '500', '750', '1000'
        ],
      },
      currentValue: '100',
      currentTarget: null,

      tags: [
        {
          name: 'Visitor parks',
          value: 10
        },
        {
          name: 'Casual parks',
          value: 40
        }, 
        {
          name: 'Allocated parks',
          value: 50
        }
      ],

      settings: {
        sliderWidth: null,
        start: 0,
        end: null,
        newValues: null,
      },

      dragging: false,
      doDragging: false,

      calcValues: [],
    }
  },

  emits: ['dropValues', 'tBays'],
  
  mounted() {
    this.settings.sliderWidth = this.$refs.bigSlider.offsetWidth
    
    this.settings.newValues = this.tags.map((tag) => tag.value)
    
    this.stopDrag()
    this.calculateValues()
  },

  updated() {
    if (!this.dragging) {
      this.calculateValues()
    }
  },

  methods: {
    clamp(value, min, max) {
      return Math.min(Math.max(value, min), max)
    },

    getPercent(containerWidth, distanceMoved) {
      return (distanceMoved / containerWidth) * 100
    },

    nearestMultiple(divisor, number) {
      return Math.ceil(number / divisor) * divisor
    },

    setFirstSliderValue(value) {
      this.currentValue = this.firstSlider.range[value]

      return this.$emit('tBays', this.currentValue)
    },

    calculateValues() {
      let values = [...this.settings.newValues]
      this.calcValues = values

      this.calcValues.forEach((item, idx) => {
        this.calcValues[idx] = Math.round((this.currentValue / 100) * item)
      })

      return this.$emit('dropValues', this.calcValues)
    },

    startDrag(event) {
      this.dragging = true
      this.currentTarget = event.target
      this.settings.start = event.pageX
    },

    stopDrag() {
      this.dragging = false
      
      if (this.doDragging) {
        this.tags.forEach((tag, idx) => {
          tag.value = this.settings.newValues[idx]
        })
        this.doDragging = false
      }
    },

    doDrag(event) {
      if (this.dragging) {
        this.doDragging = true
        this.changeSliderValues(event.target, event.pageX)
      }
    },

    changeSliderValues(target, end) {
      let tagIndex = parseInt(  
        target
          .closest('.calc__big-slider-tag')
          .getAttribute('data-tag-index')
      )
      
      let values = this.tags.map((tag) => tag.value)
      let startDragX = this.settings.start

      let endDragX = end
      let distanceMoved = endDragX - startDragX

      values = this.tags.map((tag) => tag.value) // read in updated values
      let maxValue = values[tagIndex] + values[tagIndex + 1]
      let valueMoved = this.nearestMultiple(1, this.getPercent(this.settings.sliderWidth, distanceMoved))
      let prevValue = values[tagIndex]
      let newValue = prevValue + valueMoved

      let currTagValue = this.clamp(newValue, 0, maxValue)
      values[tagIndex] = currTagValue

      let nextTagIndex = tagIndex + 1
      let nextTagNewValue = values[nextTagIndex] - valueMoved
      let nextTagValue = this.clamp(nextTagNewValue, 0, maxValue)
      values[nextTagIndex] = nextTagValue

      Array.from(document.querySelectorAll('.calc__big-slider-tag')).forEach((tagElement) => {
        let tagIndex = parseInt(tagElement.getAttribute('data-tag-index'))
        let value = values[tagIndex]

        tagElement.style.width = value + '%'
        tagElement.querySelector('span').textContent = value + '%'
      })
      
      this.settings.end = endDragX
      this.settings.newValues = values
    }
  },

}
</script>
