<template>
  <svg class="card">
    <svg:style type="text/css">
      .card-text {
        text-anchor: middle;
        font-size: 12px;
      }
    </svg:style>
    <rect :x="this.x" :y="this.y" width="140" height="48" rx="10" ry="10" :fill="this.fill" class="card-background" v-on:click="click" />
    <text :x="this.textX" :y="this.textY" class="card-text">{{ this.text.substr(0, 10) }}</text>
    <text v-if="this.isLongText" :x="this.textX" :y="Number(this.textY) + 20" class="card-text"> {{ this.text.substr(10, 10) }}</text>
  </svg>
</template>
<script>
export default {
  name: 'Card',
  props: ['x', 'y', 'fill', 'text'],
  computed: {
    isLongText () {
      return this.text.length > 10
    },
    textX () {
      return Number(this.x) - 30 + 100
    },
    textY () {
      return Number(this.y) + (this.isLongText ? 20 : 30)
    }
  },
  methods: {
    created: function () {
      console.log(this.x)
      console.log(this.y)
    },
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
    }
  }
}
</script>
<style>
</style>
