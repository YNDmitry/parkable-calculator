<template>
  <div class="calc__big-slider-wrapper">
    <div class="calc__big-slider-head">
      <div>Allocated Parks</div>
      <div>Casual Parks</div>
      <div>Visitor Parks</div>
    </div>
    <div class="calc__big-slider">
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
        <span>{{ Math.ceil(tag.value) }}%</span>
        <div class="slider-handle" 
          :data-tag="tag.name" 
          @mousedown="startDrag" 
          @mousemove="doDrag"
          @mouseup="stopDrag"
        ></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tags: [
        {
          name: 'Allocated Parks',
          value: 50
        }, {
          name: 'Casual Parks',
          value: 40
        }, {
          name: 'Visitor Parks',
          value: 10
        }
      ],
      settings: {
        sliderWidth: null,
        start: 0,
        newValues: null,
      },
      dragging: false,

      totalBays: 300,
      calcValues: [],
    }
  },

  emits: ['dropValues'],

  updated() {
    this.calculateValues()
  },
  
  mounted() {
    const sliderElement = document.querySelector('.calc__big-slider')
    this.settings.sliderWidth = sliderElement.offsetWidth
    
    this.settings.newValues = this.tags.map((tag) => tag.value)
    
    this.stopDrag()
    this.calculateValues()
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

    calculateValues() {
      this.calcValues = this.settings.newValues

      this.calcValues.forEach((item, idx) => {
        this.calcValues[idx] = this.totalBays / 100 * item
      })

      return this.$emit('dropValues', this.calcValues)
    },

    startDrag(event) {
      this.dragging = true;
      this.settings.start = event.pageX
    },

    stopDrag() {
      this.dragging = false;

      this.tags.forEach((tag, idx) => {
        tag.value = this.settings.newValues[idx]
      })

    },

    doDrag(event) {
      if (this.dragging) {
        let tagIndex = parseInt(
          document.querySelector(`[data-tag="${event.target.getAttribute('data-tag')}"]`)
            .closest('.calc__big-slider-tag').getAttribute('data-tag-index')
        )

        let values = this.tags.map((tag) => tag.value)
        let startDragX = this.settings.start

        let endDragX = event.pageX
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

        this.settings.newValues = values
      }
    },
  },

}
</script>
