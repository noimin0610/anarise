<template>
  <div id="app">
    <h1>Anarise</h1>
    <router-view/>
    <h2>Anarise のつかいかた</h2>
    <p>3つのカテゴリにカードを追加して，カードを繋げて，自己分析を助けるツールです</p>
    <ol>
      <li>"+"ボタンでそのカテゴリにカードを追加</li>
      <li>カード左上の"x"ボタンでカードを削除</li>
      <li>カード右上の"edit"ボタンでカードのテキストを編集</li>
      <li>2枚のカードを選択するとカードの間に線を追加</li>
      <li>カード間の線をクリックすると線を削除</li>
    </ol>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #333;
  margin-top: 60px;
}
h1 {
  font-size: 48px;
}
ol {
  width: 600px;
  margin: auto;
}
li {
  text-align: left;
}
</style>
