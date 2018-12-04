<template>
  <div class="hello">
    <svg viewBox="0 0 600 400">
      <svg:style type="text/css">
        text {
          fill: #333;
        }
        .category-title {
          text-anchor: middle;
          font-size: 20px;
        }
        .selected {
          stroke: #333;
          stroke-width: 3;
        }
        line {
          stroke: #333;
          stroke-width: 3;
        }
      </svg:style>

      <svg:defs>
        <g id="category-area">
          <rect x="inherit" y="0" width="200" height="400"  fill="inherit" class="category" />
        </g>
      </svg:defs>

      <use x="0" fill="aquamarine" href="#category-area" />
      <text x="100" y="20" class="category-title">エピソード・経験</text>
      <rect x="30" y="50" width="140" height="50" rx="10" ry="10" fill="mediumseagreen" class="card" v-on:click="select" />
      <text x="100" y="80" text-anchor="middle">競プロ</text>

      <use x="200" fill="lightskyblue" href="#category-area" />
      <text x="300" y="20" class="category-title">性質・スキル・能力</text>
      <rect x="230" y="50" width="140" height="50" rx="10" ry="10" fill="cornflowerblue" class="card" v-on:click="select" />
      <text x="300" y="80" text-anchor="middle">実装力</text>

      <use x="400" fill="pink" href="#category-area" />
      <text x="500" y="20" class="category-title">志望先に求めるもの</text>
      <rect x="430" y="50" width="140" height="50" rx="10" ry="10" fill="hotpink" class="card" v-on:click="select" />
      <text x="500" y="80" text-anchor="middle">手が動かせる</text>
    </svg>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      selectCount: 0,
      selectItems: [],
      edges: [],
      timerID: null
    }
  },
  methods: {
    select: function (e) {
      let target = e.currentTarget
      let newItem = {
        element: target,
        x: target.x.baseVal.value,
        y: target.y.baseVal.value,
        width: target.width.baseVal.value,
        height: target.height.baseVal.value
      }

      if (target.classList.contains('selected')) {
        target.classList.remove('selected')
        this.selectCount -= 1
        this.selectItems.splice(
          this.selectItems.indexOf(newItem), 1
        )
      } else {
        target.classList.add('selected')
        this.selectCount += 1
        this.selectItems.push(newItem)
        console.log(this)
        if (this.selectCount === 2) {
          this.writeLine()
          this.runTimer()
        }
      }
      console.log(this.selectCount)
    },
    writeLine: function () {
      var line = document.createElementNS('http://www.w3.org/2000/svg', 'line')
      if (this.selectItems[0].x > this.selectItems[1].x) {
        var t = this.selectItems[0]
        this.selectItems[0] = this.selectItems[1]
        this.selectItems[1] = t
      }
      line.setAttribute('x1', this.selectItems[0].x + this.selectItems[0].width)
      line.setAttribute('y1', (this.selectItems[0].y + this.selectItems[0].height / 2))
      line.setAttribute('x2', this.selectItems[1].x)
      line.setAttribute('y2', (this.selectItems[1].y + this.selectItems[1].height / 2))
      document.getElementsByTagName('svg')[0].appendChild(line)

      this.edges.push({
        element: line,
        nodes: [this.selectItems[0], this.selectItems[1]]
      })
    },
    clearSelect: function () {
      clearTimeout(this.timerID)
      this.selectCount = 0
      this.selectItems[0].element.classList.remove('selected')
      this.selectItems[1].element.classList.remove('selected')
      this.selectItems = []
    },
    runTimer: function () {
      this.timerID = setTimeout(this.clearSelect, 300)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
svg {
  height: 600px;
  width: 900px;
}
</style>
