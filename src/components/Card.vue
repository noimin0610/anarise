<template>
  <svg class="card">
    <svg:style type="text/css">
      .card-text {
        text-anchor: middle;
        font-size: 12px;
      }
    </svg:style>
    <rect :x="this.x" :y="this.y" width="140" height="48" rx="10" ry="10" :fill="this.fill" class="card-background" v-on:click="select" />
    <text :x="this.textX" :y="this.textY" class="card-text">{{ this.text.substr(0, 10) }}</text>
    <text v-if="this.isLongText" :x="this.textX" y="90" class="card-text"> {{ this.text.substr(10, 10) }}</text>
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
      if (this.isLongText) {
        return 70
      } else {
        return 80
      }
    }
  }
}
</script>
<style>
</style>
