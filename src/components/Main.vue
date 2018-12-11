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
          stroke-width: 1;
        }
        line {
          stroke: #333;
          stroke-width: 5;
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
      <svg v-for="(card, index) in cards[0]" :key="card.id" class="card-category0">
        <Card x="30" :y="50 * (index + 1)" :fill="card.fill" :text="card.text" :index="index" />
      </svg>

      <use x="200" fill="lightskyblue" href="#category-area" />
      <text x="300" y="20" class="category-title">性格・スキル・能力</text>
      <use cx="300" x="300" fill="cornflowerblue" href="#add-button" v-on:click="addCard" />
      <svg v-for="(card, index) in cards[1]" :key="card.id" class="card-category1">
        <Card x="230" :y="50 * (index + 1)" :fill="card.fill" :text="card.text" :index="index" />
      </svg>

      <use x="400" fill="pink" href="#category-area" />
      <text x="500" y="20" class="category-title">志望先の特徴</text>
      <use cx="500" x="500" fill="hotpink" href="#add-button" v-on:click="addCard" />
      <svg v-for="(card, index)  in cards[2]" :key="card.id" class="card-category2">
        <Card x="430" :y="50 * (index + 1)" :fill="card.fill" :text="card.text" :index="index" />
      </svg>

      <svg v-for="(line, index) in lines" :key="line.key">
        <svg:line :x1="line.x1" :y1="line.y1" :x2="line.x2" :y2="line.y2" :index="index" v-on:click="removeLine" />
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
            index: 0,
            fill: 'mediumseagreen',
            text: 'X社インターンでYの改良をした'
          }
        ], [
          {
            index: 0,
            fill: 'cornflowerblue',
            text: 'RDB設計'
          }, {
            index: 1,
            fill: 'cornflowerblue',
            text: '複雑な実装が得意'
          }, {
            index: 2,
            fill: 'cornflowerblue',
            text: '内省的'
          }
        ], [
          {
            index: 0,
            fill: 'hotpink',
            text: '尖った技術力'
          }
        ]
      ],
      cardLimit: 7,
      selectCount: 0,
      selectItems: [],
      lines: [],
      lineKey: 0,
      timerID: null
    }
  },
  components: {
    Card
  },
  methods: {
    unselect: function (clickedItem) {
      clickedItem.target.classList.remove('selected')
      clickedItem.object.isSelected = false
      this.selectCount -= 1
      this.selectItems.splice(
        this.selectItems.indexOf(clickedItem), 1
      )
    },
    select: function (clickedItem) {
      clickedItem.target.classList.add('selected')
      clickedItem.object.isSelected = true
      this.selectCount += 1
      this.selectItems.push(clickedItem)
      if (this.selectCount === 2) {
        this.writeLine()
        this.runTimer()
      }
    },
    writeLine: function () {
      if (Math.abs(this.selectItems[0].x - this.selectItems[1].x) !== 200) {
        return
      }
      if (this.selectItems[0].x > this.selectItems[1].x) {
        let t = this.selectItems[0]
        this.selectItems[0] = this.selectItems[1]
        this.selectItems[1] = t
      }
      let line = {
        x1: this.selectItems[0].x + this.selectItems[0].width,
        y1: this.selectItems[0].y + this.selectItems[0].height / 2,
        x2: this.selectItems[1].x,
        y2: this.selectItems[1].y + this.selectItems[1].height / 2,
        key: this.lineKey
      }
      for (let i in this.lines) {
        if (line.x1 === this.lines[i].x1 && line.x2 === this.lines[i].x2 && line.y1 === this.lines[i].y1 && line.y2 === this.lines[i].y2) {
          return
        }
      }
      this.lineKey += 1
      this.lines.push(line)
    },
    clearSelect: function () {
      clearTimeout(this.timerID)
      this.selectCount = 0
      this.selectItems[0].target.classList.remove('selected')
      this.selectItems[1].target.classList.remove('selected')
      this.selectItems[0].object.isSelected = false
      this.selectItems[1].object.isSelected = false
      this.selectItems = []
    },
    runTimer: function () {
      this.timerID = setTimeout(this.clearSelect, 300)
    },
    removeLine: function (e) {
      for (let i in this.lines) {
        console.log(this.lines[i].index)
      }
      const target = e.currentTarget
      this.lines.splice(target.attributes.index.nodeValue, 1)
      for (let i in this.lines) {
        this.lines[i].index = i
      }
      for (let i in this.lines) {
        console.log(this.lines[i].index)
      }
    },
    addCard: function (e, isSame) {
      const target = e.currentTarget
      let text
      if (isSame) {
        text = window.prompt('20文字以内で入力してください．同じカテゴリに同じ名前のカードは作れません．', '')
      } else {
        text = window.prompt('20文字以内で入力してください', '')
      }
      if (text.length === 0 || text.length > 20) {
        this.addCard(e, false)
        return
      }
      const categoryIdx = Math.floor(target.x.baseVal.value / 200)
      if (this.cards[categoryIdx].some(card => card.text === text)) {
        this.addCard(e, true)
        return
      }
      const cardIdx = this.cards[categoryIdx].length
      if (cardIdx >= this.cardLimit) {
        target.parentNode.removeChild(target)
      }
      this.cards[categoryIdx].push({
        index: this.cards[categoryIdx].length,
        fill: (categoryIdx === 0 ? 'mediumseagreen' : (categoryIdx === 1 ? 'cornflowerblue' : 'hotpink')),
        text: text
      })
    },
    removeCard: function (index, category) {
      this.cards[category].splice(index, 1)
      for (let i in this.cards[category]) {
        this.cards[category][i].index = i
      }
      this.lines = this.lines.filter(e => !(e.x1 === 200 * category + 170 && e.y1 === 50 * (index + 1) + 24) && !(e.x2 === 200 * category + 30 && e.y2 === 50 * (index + 1) + 24))

      for (let i in this.lines) {
        let e = this.lines[i]
        if (e.x1 === 200 * category + 170 && e.y1 > 50 * (index + 1) + 24) {
          this.lines[i].y1 -= 50
        }
        if (e.x2 === 200 * category + 30 && e.y2 > 50 * (index + 1) + 24) {
          this.lines[i].y2 -= 50
        }
      }
    },
    editCard: function (index, category, isSame) {
      let text
      if (isSame) {
        text = window.prompt('20文字以内で入力してください．同じカテゴリに同じ名前のカードは作れません．', this.cards[category][index].text)
      } else {
        text = window.prompt('20文字以内で入力してください', this.cards[category][index].text)
      }
      if (text.length === 0 || text.length > 20) {
        this.addCard(index, category, false)
        return
      }
      if (this.cards[category].some(card => card.text === text)) {
        this.addCard(e, true)
        return
      }
      this.cards[category][index].text = text
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
