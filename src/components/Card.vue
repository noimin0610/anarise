<template>
  <svg class="card">
    <svg:style type="text/css">
      .card-text {
        text-anchor: middle;
        font-size: 12px;
      }
      text.remove-button {
        font-weight: bold;
        cursor: pointer;
        fill: white;
      }
    </svg:style>
    <rect :x="this.x" :y="this.y" width="140" height="48" rx="10" ry="15" :fill="this.fill" class="card-background" v-on:click="click" />
    <text :x="Number(this.x) + 5" :y="Number(this.y) + 12" class="remove-button" v-on:click="removeCard">×</text>
    <text :x="this.textX" :y="this.textY" class="card-text">{{ this.text.substr(0, 10) }}</text>
    <text v-if="this.isLongText" :x="this.textX" :y="Number(this.textY) + 20" class="card-text"> {{ this.text.substr(10, 10) }}</text>
  </svg>
</template>
<script>
export default {
  name: 'Card',
  props: ['index', 'x', 'y', 'fill', 'text'],
  computed: {
    isLongText () {
      return this.text.length > 10
    },
    textX () {
      return Number(this.x) - 30 + 100
    },
    textY () {
      return Number(this.y) + (this.isLongText ? 20 : 30)
    },
    isSelected () {
      const rect = document.getElementsByTagName('rect')[0]
      return rect.classList.contains('selected')
    },
    category () {
      if (this.fill === 'mediumseagreen') {
        return 0
      } else if (this.fill === 'cornflowerblue') {
        return 1
      }
      return 2
    }
  },
  methods: {
    click: function (e) {
      const target = e.currentTarget
      const newItem = {
        element: target,
        x: target.x.baseVal.value,
        y: target.y.baseVal.value,
        width: target.width.baseVal.value,
        height: target.height.baseVal.value
      }
      if (target.classList.contains('selected')) {
        target.classList.remove('selected')
        this.$parent.unselect(target, newItem)
      } else {
        target.classList.add('selected')
        this.$parent.select(target, newItem)
      }
    },
    removeCard: function (e) {
      // const target = e.target.parentElement.parentElement
      // this.$parent.removeCard(target, this.category)
      this.$parent.removeCard({
        fill: this.fill,
        text: this.text
      }, this.category)
    }
  }
}
</script>
<style scoped>
.card {
  cursor: pointer;
}
</style>
