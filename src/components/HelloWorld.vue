<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
      <h2 v-show="showText">{{ text }}</h2>
      <button  v-on:click="toggle">toggle</button>

    <svg viewBox="0 0 600 400">
      <svg:style type="text/css">
        .category-title {
          text-anchor: middle;
          font-size: 20px;
        }
      </svg:style>
      <svg:defs>
        <g id="category-area">
          <rect x="inherit" y="0" width="200" height="400"  fill="inherit" class="category" />
        </g>
      </svg:defs>
      <use x="0" fill="lightskyblue" href="#category-area" />
      <text x="100" y="20" class="category-title">エピソード・経験</text>

      <use x="200" fill="lemonchiffon" href="#category-area" />
      <text x="300" y="20" class="category-title">性質・スキル・能力</text>
      <use x="250" y="50" width="100" height="50" rx="10" ry="10" fill="gold" />
      <text x="300" y="80" text-anchor="middle">可愛がり</text>

      <use x="400" fill="pink" href="#category-area" />
      <text x="500" y="20" class="category-title">志望先に求めるもの</text>
    </svg>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Hello Vue.js',
      text: 'にほngo',
      showText: true
    }
  },
  methods: {
    toggle: function () {
      this.showText = !this.showText
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
svg {
  height: 600px;
  width: 900px;
}
</style>
