<template>
  <WindowFrame titleText="マスク変更" display-property="private.display.editMapMaskWindow" align="center" fixSize="285, 198" @open="initWindow" @reset="initWindow">
    <table>
      <tbody>
        <tr>
          <th>文字：</th>
          <td><input type="text" v-model="name"></td>
          <td rowspan="6" class="mapMaskGrid"><div class="mapMask" :style="mapMaskStyle">{{name}}</div></td>
        </tr>
        <tr>
          <th>色：</th>
          <td><input type="color" v-model="color"></td>
        </tr>
        <tr>
          <th>高さ：</th>
          <td><input type="number" min="1" v-model="height"></td>
        </tr>
        <tr>
          <th>幅：</th>
          <td><input type="number" min="1" v-model="width"></td>
        </tr>
        <tr>
          <th>透明度：</th>
          <td><input type="range" v-model="transparency"></td>
        </tr>
        <tr>
          <td colspan="2" class="multi"><button @click="commitEdit">編集</button><button @click="cancelEdit">キャンセル</button></td>
        </tr>
      </tbody>
    </table>
  </WindowFrame>
</template>

<script>
import { mapState, mapActions, mapGetters } from 'vuex'
import WindowFrame from '../../WindowFrame'
import WindowMixin from '../../WindowMixin'

export default {
  name: 'editMapMaskWindow',
  mixins: [WindowMixin],
  components: {
    WindowFrame
  },
  data () {
    return {
      name: '',
      width: 1,
      height: 1,
      color: '#000000',
      transparency: 0
    }
  },
  methods: {
    ...mapActions([
      'windowClose',
      'changePieceInfo'
    ]),
    commitEdit () {
      this.changePieceInfo({ propName: 'mapMask', key: this.key, name: this.name, columns: this.width, rows: this.height, color: this.rgba, fontColor: this.fontColor, isNotice: true })
      this.windowClose('private.display.editMapMaskWindow')
    },
    cancelEdit () {
      this.windowClose('private.display.editMapMaskWindow')
    },
    initWindow () {
      // console.qLog(`initWindow`)
      let mapMaskObj = this.getPieceObj('mapMask', this.key)
      this.name = mapMaskObj.name
      this.width = mapMaskObj.columns
      this.height = mapMaskObj.rows
      const colorObj = this.parseColor(mapMaskObj.color)
      this.color = colorObj.getColorCode()
      this.transparency = 100 - Math.floor(colorObj.a * 100)
      console.qLog(`  [methods] init window => EditMapMask:{name:"${this.name}", color:${this.color}, size:(${this.width}, ${this.height}), transparency:${this.transparency}}`)
    }
  },
  computed: mapState({
    ...mapGetters([
      'parseColor',
      'getPieceObj'
    ]),
    key: state => state.private.display['editMapMaskWindow'].key,
    mapMaskStyle () {
      let width = this.width * this.gridSize
      let height = this.height * this.gridSize
      let zoom = 1
      if (Math.max(width, height) > 160) {
        zoom = 160 / Math.max(width, height)
        width *= zoom
        height *= zoom
      }
      return {
        width: width + 'px',
        height: height + 'px',
        'background-color': this.rgba,
        color: this.fontColor
      }
    },
    rgba () {
      const colorObj = this.parseColor(this.color)
      colorObj.a = (100 - this.transparency) / 100
      return colorObj.getRGBA()
    },
    fontColor () { return this.parseColor(this.color).getColorCodeReverse() },
    gridSize: state => state.public.map.grid.size
  })
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
table {
  font-size: 12px;
  white-space: nowrap;
  -moz-user-select: none;
  -webkit-user-select: none;
  -ms-user-select: none;
  border-collapse: collapse;
}
th, td {
  padding: 0;
  border: none;
}
th {
  text-align: right;
  font-weight: normal;
}
td.multi {
  text-align: center;
}
td.mapMaskGrid {
  width: 161px;
  height: 161px;
  max-width: 161px;
  max-height: 161px;
  text-align: center;
  border: none;
}
.mapMask {
  max-width: 157px;
  max-height: 157px;
  margin: auto;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 12px;
  border: solid yellow 2px;
}
input[type=number] {
  width: 46px;
}
input[type=text] {
  width: 60px;
}
input[type=range] {
  width: 60px;
}
</style>
