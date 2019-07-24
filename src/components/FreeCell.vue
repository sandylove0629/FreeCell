<template>
  <div class="content flex">
    <!--{{underBlock}}-->
    <div class="bg-block">
      <div class="ice-mountain">
        <img src="@/assets/element/element_ice.svg">
      </div>
    </div>
    <div class="under-block">
      <div class="line-block" v-for="block in underBlock">
        <div class="blank-card"></div>
        <draggable :list="block.block" group="pLine" @change="log"
                   :move="movePoker" @start="curList = block.block" class="h-100-p">
          <div
                  class="poker"
                  v-for="(poker, index) in block.block"
                  :key="poker.num"
          >
            <img class="poker-card" :src="require(`@/assets/card/${poker.num}.svg`)">
            <!--{{transformCard(poker.num)}} {{index}}-->
          </div>
        </draggable>
      </div>
      <!--
      <draggable v-model="underBlock">
        <transition-group class="flex">
          <div class="line-block" v-for="block in underBlock" :key="block.line">
            <div v-for="poker in block.block">
              <img class="poker-card" :src="require(`@/assets/card/${poker.num}.svg`)">
              {{transformCard(poker.num)}}
            </div>
          </div>
        </transition-group>
      </draggable>
      -->
      <!--
      <div class="line-block" v-for="block in underBlock">
        <div v-for="poker in block">
          <img class="poker-card" :src="require(`@/assets/card/${poker}.svg`)">
          {{transformCard(poker)}}
        </div>
      </div>
      -->
    </div>
    <div class="footer-block flex">
      <div class="left-block flex">
        <div class="blank-card" v-for="index in 4"></div>
      </div>
      <div class="setting-block">
        <div class="flex text-center">
          <div class="btn-setting">
            <img src="@/assets/element/element-14.svg">
          </div>
          <div class="btn-setting">
            <img src="@/assets/element/element-16.svg">
          </div>
          <div class="btn-setting">
            <img src="@/assets/element/element-15.svg">
          </div>
        </div>
        <div class="text-left text">
          Score&nbsp;|&nbsp;000
        </div>
        <div class="text-left text">
          Time&nbsp;&nbsp;|&nbsp;00:00
        </div>
      </div>
      <div class="right-block flex">
        <div class="blank-card right-card" v-for="index in 4"></div>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from 'vuedraggable'
export default {
  name: 'FreeCell',
  components: {
    draggable
  },
  data () {
    return {
      hello: '你好',
      leftBlock: ['', '', '', ''], // 左存放
      rightBlock: [[], [], [], []], // 右存放
      underBlock: [], // 下方8行，7、7、7、7、6、6、6、6
      timeMachine: [],
      ignoreCard: [], // 預設右存放
      onDragNum: null, // 拖移
      onDropNum: null, // 放下
      onDragDeck: null, // 拖移的牌區
      onDropDeck: null, // 放下的牌區
      isCacheDeck: false, // 左存放的動作
      cacheDeckNum: null, // 左存放的牌區
      isSuccessDeck: false, // 右存放的動作
      successDeckNum: null, // 右存放的牌區
      gamePause: false, // 暫停狀態
      //
      curList: [] // 拖移的行
    }
  },
  created () {
    this.createRandom()
  },
  methods: {
    createRandom () {
      let blockLength = [7, 7, 7, 7, 6, 6, 6, 6]
      let i = 0
      let bl = blockLength[i]
      for (let j = 0; j < this.shufflePoker.length; j+=bl) {
        let obj = {
          block: this.shufflePoker.slice(j, j + bl),
          line: i + 1
        }
        this.underBlock.push(obj)
        bl = blockLength[i++]
      }
    },
    transformCard (num) {
      let index = num % 13
      let color = ''
      if (!index) index = 13
      if (num >= 1 && num <= 13) color = 'c' // club
      else if (num >= 14 && num <= 26) color = 'd' // diamond
      else if (num >= 27 && num <= 39) color = 'h' // heart
      else color = 's' // spade
      return color + index
    },
    checkLast (line, index) {
      console.log(line.length - 1)
      if (line.length === 6) return true
      else return false
    },
    movePoker (evt, originalEvent) {
      // 移動的內容
      // console.log(evt.draggedContext.element)
      // 每排最後一個element才可移動
      if (this.curList.length) {
        const last = this.curList[this.curList.length - 1]
        if (last.num === evt.draggedContext.element.num) return true
      }
      return false
    },
    log (e) {
      console.log(e)
    }
  },
  computed: {
    poker () {
      let poker = []
      for (let i = 0; i < 52; i++) {
        let obj = {
          num: i + 1,
          color: this.transformCard(i + 1)
        }
        poker.push(obj)
      }
      return poker
    },
    shufflePoker () {
      let i, j, temp
      let arr = this.poker
      for (i = arr.length - 1; i > 0; i--) {
        j = Math.floor(Math.random() * (i + 1))
        temp = arr[i]
        arr[i] = arr[j]
        arr[j] = temp
      }
      return arr
    }
  }
}
</script>

<style lang="sass">
$card-radius: 5px
$card-p-t: 18px
$card-p-r: 55px
$d-color: #2A4254
$r-card-text: #282828
@font-face
  font-family: 'Noto Sans TC'
  font-style: normal
  font-weight: 100
  src: url(//fonts.gstatic.com/ea/notosanstc/v1/NotoSansTC-Black.woff2)
*
  box-sizing: border-box
  font-family: 'Noto Sans TC', '微軟正黑體', sans-serif
body, html, #app
  background-color: $d-color
  height: 100%
  width: 100%
  margin: 0 !important
  padding: 0 !important
.content
  height: 100%
  width: 100%
  padding-top: 20px
  text-align: center
  justify-content: center
.flex
  display: flex
.flex-center
  justify-content: center
.block
  display: block
.text-left
  text-align: left
.text-center
  text-align: center
.h-100-p
  height: 100%
.blank-card
  width: 94.95px
  height: 125px
  border: 3px solid #fafafa
  border-radius: $card-radius
.poker
  position: relative
  width: 100%
  padding: $card-p-t $card-p-r
  .poker-card
    border-radius: $card-radius
    box-shadow: 0px -1px 3px #888888
    height: 125px
    position: absolute
    left: 0
    background: white
.under-block
  display: flex
  height: 60%
  .line-block
    width: $card-p-r * 2
    .blank-card
      position: absolute
      margin-top: $card-p-t
.bg-block
  height: 100%
  width: 100%
  position: fixed
  bottom: 120px
  .ice-mountain
    width: 40%
    position: absolute
    bottom: 0
    right: 10%
.footer-block
  height: 130px
  width: 100%
  background-color: #C7E7FF
  position: fixed
  bottom: 0
  border-top: solid 10px #E9F5FE
  padding: 15px
  justify-content: center
  align-items: flex-end
  .left-block, .right-block
    position: relative
    top: 0px
    font-weight: 200
  .blank-card
    margin: 0px 5px
  .setting-block
    padding: 0px 20px
  .text
    color: $d-color
    font-size: 20px
    font-weight: bold
    padding: 0px 10px
  .right-card
    background-color: rgba(255, 255, 255, 0.3)
  .right-card::after
    content: 'A'
    display: block
    color: rgba($r-card-text, 0.3)
    font-size: 40px
    padding-top: 30px
    font-weight: bold
.btn-setting
  padding: 0px 5px
  img
    width: 40px
</style>
