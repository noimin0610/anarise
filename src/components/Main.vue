<template>
  <div class="main">
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
        .card-text {
          text-anchor: middle;
          font-size: 12px;
        }
        line {
          stroke: #333;
          stroke-width: 3;
        }
      </svg:style>

      <svg:defs>
        <g id="category-area">
          <rect y="0" width="200" height="400"  fill="inherit" class="category" />
        </g>
        <g id="add-button">
          <circle cy="35" r="10" fill="inherit" />
          <text y="40" text-anchor="middle">
            <tspan font-weight="bold" fill="white">+</tspan>
          </text>
        </g>
      </svg:defs>

      <use x="0" fill="aquamarine" href="#category-area" />
      <text x="100" y="20" class="category-title">エピソード・経験</text>
      <use cx="100" x="100" fill="mediumseagreen" href="#add-button" v-on:click="addCard" />
      <rect x="30" y="50" width="140" height="48" rx="10" ry="10" fill="mediumseagreen" class="card" v-on:click="select" />
      <text x="100" y="70" class="card-text">＊＊ちゃんとの交際＊＊</text>
      <text x="100" y="90" class="card-text">＊＊ちゃんとの交際＊＊</text>

      <use x="200" fill="lightskyblue" href="#category-area" />
      <text x="300" y="20" class="category-title">性格・スキル・能力</text>
      <use cx="300" x="300" fill="cornflowerblue" href="#add-button" v-on:click="addCard" />
      <rect x="230" y="50" width="140" height="48" rx="10" ry="10" fill="cornflowerblue" class="card" v-on:click="select" />
      <text x="300" y="80" class="card-text" >ショタコン</text>
      <rect x="230" y="100" width="140" height="48" rx="10" ry="10" fill="cornflowerblue" class="card" v-on:click="select" />
      <text x="300" y="130" class="card-text">積極的</text>

      <use x="400" fill="pink" href="#category-area" />
      <text x="500" y="20" class="category-title">志望先の特徴</text>
      <use cx="500" x="500" fill="hotpink" href="#add-button" v-on:click="addCard" />
      <rect x="430" y="50" width="140" height="48" rx="10" ry="10" fill="hotpink" class="card" v-on:click="select" />
      <text x="500" y="80" class="card-text">可愛い</text>
    </svg>
  </div>
</template>

<script>
export default {
  name: 'Main',
  data () {
    return {
      cardCount: [1, 2, 1],
      cardLimit: 7,
      selectCount: 0,
      selectItems: [],
      edges: [],
      timerID: null
    }
  },
  methods: {
    select: function (e) {
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
      let line = document.createElementNS('http://www.w3.org/2000/svg', 'line')
      if (Math.abs(this.selectItems[0].x - this.selectItems[1].x) !== 200) {
        return
      }
      if (this.selectItems[0].x > this.selectItems[1].x) {
        let t = this.selectItems[0]
        this.selectItems[0] = this.selectItems[1]
        this.selectItems[1] = t
      }
      line.setAttribute('x1', this.selectItems[0].x + this.selectItems[0].width)
      line.setAttribute('y1', (this.selectItems[0].y + this.selectItems[0].height / 2))
      line.setAttribute('x2', this.selectItems[1].x)
      line.setAttribute('y2', (this.selectItems[1].y + this.selectItems[1].height / 2))
      line.addEventListener('click', this.removeLine)
      document.getElementsByTagName('svg')[0].appendChild(line)

      this.edges.push(line)
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
    },
    removeLine: function (e) {
      const target = e.currentTarget
      const newEdges = this.edges.filter(e => e !== target)
      this.edges = newEdges
      console.log(this.edges)
      target.parentNode.removeChild(target)
    },
    addCard: function (e) {
      const target = e.currentTarget
      const text = window.prompt('22文字以内で入力してください', '')
      if (text.length === 0 || text.length > 22) {
        this.addCard(e)
        return
      }
      const categoryIdx = Math.floor(target.x.baseVal.value / 200)
      this.cardCount[categoryIdx]++
      const cardIdx = this.cardCount[categoryIdx]

      if (cardIdx === this.cardLimit) {
        target.parentNode.removeChild(target)
      }

      let card = document.createElementNS('http://www.w3.org/2000/svg', 'rect')
      card.setAttribute('x', 200 * categoryIdx + 30)
      card.setAttribute('y', 50 * cardIdx)
      card.setAttribute('width', 140)
      card.setAttribute('height', 48)
      card.setAttribute('rx', 10)
      card.setAttribute('ry', 10)
      card.setAttribute('fill', (categoryIdx === 0 ? 'mediumseagreen' : (categoryIdx === 1 ? 'cornflowerblue' : 'hotpink')
      ))
      card.classList.add('card')
      card.addEventListener('click', this.select)
      document.getElementsByTagName('svg')[0].appendChild(card)
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
