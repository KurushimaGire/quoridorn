<template>
  <WindowFrame titleText="隠しダイスロール結果" display-property="private.display.secretDiceWindow" align="center" baseSize="300, 10">
    <div class="contents" v-for="(obj, index) in results" :key="index">
      <textarea v-model="obj.text" title=""></textarea>
      <button @click="clickOpen(obj.key)">結果公開</button>
    </div>
  </WindowFrame>
</template>

<script>
import WindowFrame from './WindowFrame'
import WindowMixin from '../WindowMixin'

export default {
  mixins: [WindowMixin],
  components: {
    WindowFrame
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .contents {
    position: relative;
    height: 100%;
  }
  .contents > * {
    display: block;
    position: absolute;
    left: 0;
    right: 0;
    margin: 0 auto;
  }
  button {
    bottom: 0;
    height: 30px;
    width: 70px;
  }
</style>
