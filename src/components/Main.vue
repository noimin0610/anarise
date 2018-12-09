<template>
  <div class="main">
    <svg id="content" viewBox="0 0 600 400">
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
        text.add-button {
          fill: white;
          font-weight: bold;
          text-anchor: middle;
          cursor: pointer;
        }
      </svg:style>

      <svg:defs>
        <g id="category-area">
          <rect y="0" width="200" height="400"  fill="inherit" class="category" />
        </g>
        <g id="add-button">
          <circle cy="35" r="10" fill="inherit" />
          <text y="40" class="add-button">+</text>
        </g>
      </svg:defs>

      <use x="0" fill="aquamarine" href="#category-area" />
      <text x="100" y="20" class="category-title">エピソード・経験</text>
      <use cx="100" x="100" fill="mediumseagreen" href="#add-button" v-on:click="addCard" />
      <svg v-for="card in cards[0]" :key="card.id" class="card-category0">
        <Card :x="card.x" :y="card.y" :fill="card.fill" :text="card.text" :id="card.id" />
      </svg>

      <use x="200" fill="lightskyblue" href="#category-area" />
      <text x="300" y="20" class="category-title">性格・スキル・能力</text>
      <use cx="300" x="300" fill="cornflowerblue" href="#add-button" v-on:click="addCard" />
      <svg v-for="card in cards[1]" :key="card.id" class="card-category1">
        <Card :x="card.x" :y="card.y" :fill="card.fill" :text="card.text" :id="card.id" />
      </svg>

      <use x="400" fill="pink" href="#category-area" />
      <text x="500" y="20" class="category-title">志望先の特徴</text>
      <use cx="500" x="500" fill="hotpink" href="#add-button" v-on:click="addCard" />
      <svg v-for="card in cards[2]" :key="card.id" class="card-category2">
        <Card :x="card.x" :y="card.y" :fill="card.fill" :text="card.text" :id="card.id" />
      </svg>
    </svg>
  </div>
</template>

<script>
import Card from './Card'
export default {
  name: 'Main',
  data () {
    return {
      cards: [
        [
          {
            id: 0,
            x: 30,
            y: 50,
            fill: 'mediumseagreen',
            text: '競プロで青コーダーになった'
          }
        ], [
          {
            id: 7,
            x: 230,
            y: 50,
            fill: 'cornflowerblue',
            text: '自我'
          }, {
            id: 8,
            x: 230,
            y: 100,
            fill: 'cornflowerblue',
            text: '実装力'
          }, {
            id: 9,
            x: 230,
            y: 150,
            fill: 'cornflowerblue',
            text: '内省'
          }
        ], [
          {
            id: 14,
            x: 430,
            y: 50,
            fill: 'hotpink',
            text: '尖った技術力'
          }
        ]
      ],
      cardLimit: 7,
      selectCount: 0,
      selectItems: [],
      edges: [],
      timerID: null
    }
  },
  components: {
    Card
  },
  methods: {
    unselect: function (target, newItem) {
      this.selectCount -= 1
      this.selectItems.splice(
        this.selectItems.indexOf(newItem), 1
      )
    },
    select: function (target, newItem) {
      this.selectCount += 1
      this.selectItems.push(newItem)
      if (this.selectCount === 2) {
        this.writeLine()
        this.runTimer()
        return true // 線書いた
      }
      return []
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
      target.parentNode.removeChild(target)
    },
    addCard: function (e) {
      const target = e.currentTarget
      const text = window.prompt('20文字以内で入力してください', '')
      if (text.length === 0 || text.length > 20) {
        this.addCard(e)
        return
      }
      const categoryIdx = Math.floor(target.x.baseVal.value / 200)
      const cardIdx = this.cards[categoryIdx].length
      if (cardIdx >= this.cardLimit) {
        target.parentNode.removeChild(target)
      }
      this.cards[categoryIdx].push({
        id: this.cardLimit * categoryIdx + cardIdx,
        x: 200 * categoryIdx + 30,
        y: 50 * (cardIdx + 1),
        fill: (categoryIdx === 0 ? 'mediumseagreen' : (categoryIdx === 1 ? 'cornflowerblue' : 'hotpink')),
        text: text
      })
    },
    removeCard: function (target, category) {
      const idNum = Number(target.id.substr(-1, 1))
      document.getElementsByClassName('card-category' + category)[idNum % this.cardLimit].removeChild(
        document.getElementById(target.id)
      )

      let cardIdx = 0
      let y = 50
      const n = this.cards[category].length
      for (let i = 0; i < n; ++i) {
        const card = this.cards[category][i]
        if (Number(card.id) === idNum) {
          continue
        } else {
          this.cards[category].push({
            id: this.cardLimit * category + cardIdx,
            x: card.x,
            y: y,
            fill: card.fill,
            text: card.text
          })
          cardIdx += 1
          y += 50
        }
      }
      for (let i in this.cards[category]) {
        console.log(this.cards[category][i].id)
        console.log(this.cards[category][i].text)
        console.log(this.cards[category][i].y)
      }
      console.log()
      this.cards[category].splice(0, n)
      for (let i in this.cards[category]) {
        console.log(this.cards[category][i].id)
        console.log(this.cards[category][i].text)
        console.log(this.cards[category][i].y)
      }
      console.log()
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
