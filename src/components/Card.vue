<template>
  <svg class="card">
    <svg:style type="text/css">
      text.card-text {
        text-anchor: middle;
        font-size: 12px;
        pointer-events: none;
      }
      text.button {
        text-anchor: middle;
        cursor: pointer;
        fill: white;
      }
      text.remove-button {
        font-size: 16px;
        font-weight: bold;
      }
      text.edit-button {
        font-size: 10px;
      }
    </svg:style>
    <rect :x="this.x" :y="this.y" width="140" height="48" rx="10" ry="15" :fill="this.fill" class="card-background" :index="this.index" v-on:click="click" />
    <text :x="Number(this.x) + 10" :y="Number(this.y) + 12" class="button remove-button" v-on:click="removeCard" v-if="!this.isSelected">×</text>
    <text :x="Number(this.x) + 120" :y="Number(this.y) + 10" class="button edit-button" v-on:click="editCard" v-if="!this.isSelected">edit</text>
    <text :x="this.textX" :y="this.textY" class="card-text">{{ this.text.substr(0, 10) }}</text>
    <text v-if="this.isLongText" :x="this.textX" :y="Number(this.textY) + 20" class="card-text"> {{ this.text.substr(10, 10) }}</text>
  </svg>
</template>
<script>
export default {
  name: 'Card',
  data () {
    return {
      isSelected: false
    }
  },
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
      const clickedItem = {
        target: target,
        object: this,
        x: target.x.baseVal.value,
        y: target.y.baseVal.value,
        width: target.width.baseVal.value,
        height: target.height.baseVal.value
      }
      if (this.isSelected) {
        this.$parent.unselect(clickedItem)
      } else {
        this.$parent.select(clickedItem)
      }
    },
    removeCard: function (e) {
      this.$parent.removeCard(this.index, this.category)
    },
    editCard: function (e) {
      this.$parent.editCard(this.index, this.category, false)
    }
  }
}
</script>
<style scoped>
.card {
  cursor: pointer;
}
</style>
