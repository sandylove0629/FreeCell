<template>
  <div class="content flex">
    <!--{{underBlock}}-->
    <div class="bg-block">
      <div class="ice-mountain">
        <img src="@/assets/element/element_ice.svg">
      </div>
      <div class="penguin penguin-lg">
        <img src="@/assets/element/element-25.svg">
      </div>
      <div class="penguin penguin-sm">
        <img src="@/assets/element/element-26.svg">
      </div>
    </div>
    <div class="under-block">
      <div class="line-block" v-for="block in underBlock">
        <div class="blank-card"></div>
        <draggable :list="block.block" group="pLine" @change="log"
                   :move="movePoker" @start="curList = block.block" class="h-100-p">
          <div
                  class="poker"
                  v-for="poker in block.block"
          >
            <img class="poker-card" :src="require(`@/assets/card/${poker.num}.svg`)">
          </div>
        </draggable>
      </div>
    </div>
    <div class="footer-block flex">
      <div class="left-block flex">
        <div class="blank-card" v-for="block in leftBlock"></div>
      </div>
      <div class="setting-block">
        <div class="flex text-center">
          <div class="btn-setting c-pointer">
            <img src="@/assets/element/element-14.svg">
          </div>
          <div class="btn-setting">
            <img src="@/assets/element/element-16.svg">
          </div>
          <div class="btn-setting c-pointer" @click="showModal = true">
            <img src="@/assets/element/element-15.svg">
          </div>
        </div>
        <div class="text-left text">
          Score&nbsp;|&nbsp;000
        </div>
        <div class="text-left text">
          Time&nbsp;&nbsp;|&nbsp;{{showTime}}
        </div>
      </div>
      <div class="right-block flex">
        <div class="blank-card right-card"  v-for="block in rightBlock"></div>
      </div>
    </div>
    <div class="setting-modal flex" v-show="showModal">
      <div class="bg" @click="showModal = false"></div>
      <div class="card-board">
        <!--bg-penguin-->
        <div class="l-penguin">
          <img src="@/assets/element/element-12.svg">
        </div>
        <div class="r-penguin">
          <img src="@/assets/element/element-13.svg">
        </div>

        <div class="setting-block">
          <div class="close c-pointer" @click="showModal = false">
            ✕
          </div>
          <h1 class="title">FREE CELL</h1>
          <h4 class="sub-title">SAVE THE ICE</h4>
          <div>
            <button class="d-btn" @click="createRandom">Quit and Start A New Game</button>
          </div>
          <div>
            <button class="d-btn">Restart This Game</button>
          </div>
          <div>
            <button class="d-btn">Keep Playing</button>
          </div>
          <div>
            <button class="d-btn blue-btn">Practical Action</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from 'vuedraggable'
import moment from 'moment'
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
      curList: [], // 拖移的行
      showModal: false,
      start: false,
      time: 0,
      timeOut: ''
    }
  },
  created () {
    this.createRandom()
  },
  methods: {
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
    },
    createRandom () {
      this.start = false
      this.showModal = false
      this.underBlock = []
      let blockLength = [7, 7, 7, 7, 6, 6, 6, 6]
      let i = 0
      let bl = blockLength[i]
      let shufflePoker = this.shufflePoker()
      for (let j = 0; j < shufflePoker.length; j+=bl) {
        let obj = {
          block: shufflePoker.slice(j, j + bl),
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
    checkLast (line) {
      // console.log(line.length - 1)
      if (line.length === 6) return true
      else return false
    },
    movePoker (evt) {
      // 移動的內容
      // console.log(evt.draggedContext.element)
      // 每排最後一個element才可移動
      if (this.curList.length) {
        const last = this.curList[this.curList.length - 1]
        if (last.num === evt.draggedContext.element.num) return true
      }
      return false
    },
    log () {
      // console.log(e)
      if (!this.start) this.start = true
    },
    setTime () {
      const isStart = this.start
      const self = this
      if (isStart) {
        this.time++
        this.timeOut = setTimeout(function () {
          self.setTime()
        }, 1000)
      }
      else {
        this.time = 0
        clearTimeout(this.timeOut)
      }
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
    showTime () {
      if (this.time) {
        var newTime = moment.utc(this.time * 1000).format('mm:ss')
        return newTime
      }
      return '00:00'
    }
  },
  watch: {
    start () {
      this.setTime()
    },
    showModal () {
      if (this.showModal) clearTimeout(this.timeOut)
      else if (this.time) {
        this.setTime()
      }
    }
  }
}
</script>

<style lang="sass">
$card-radius: 5px
$card-p-t: 18px
$card-p-r: 55px
$d-color: #2A4254
$l-blue: #C7E7FF
$r-card-text: #282828
@font-face
  font-family: 'Noto Sans TC'
  font-style: normal
  font-weight: 800
  src: url(//fonts.gstatic.com/ea/notosanstc/v1/NotoSansTC-Black.woff2) format('woff2')
  src: url(//fonts.gstatic.com/ea/notosanstc/v1/NotoSansTC-Black.woff) format('woff')
  src: url(//fonts.gstatic.com/ea/notosanstc/v1/NotoSansTC-Black.otf) format('opentype')
@mixin shadow-top
  box-shadow: 0px -1px 3px #888888
@mixin shadow-bottom
  box-shadow: 0px 1px 3px #888888
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
.c-pointer
  cursor: pointer
.poker
  position: relative
  width: 100%
  padding: $card-p-t $card-p-r
  .poker-card
    border-radius: $card-radius
    @include shadow-top
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
.penguin
  position: absolute
  bottom: 0
  transform: rotateY(180deg)
  img
    width: 50px
  &.penguin-sm
    left: 10%
  &.penguin-lg
    left: calc(10% + 60px)
.setting-modal
  position: fixed
  height: 100vh
  width: 100vw
  background-color: rgba(256, 256, 256, 0.5)
  top: 0
  left: 0
  justify-content: center
  align-items: center
  .bg
    position: absolute
    top: 0
    left: 0
    width: 100%
    height: 100%
  .card-board
    @include shadow-bottom
    height: 400px
    width: 750px
    background-color: #fff
    border-radius: 5px
    position: relative
    overflow: hidden
    .title
      letter-spacing: 10px
      font-weight: bold
      margin-bottom: 10px
      line-height: 25px
    .sub-title
      letter-spacing: 5px
      margin: 0px 0px 10px 0px
      font-weight: bold
    .l-penguin, .r-penguin
      position: absolute
      bottom: -20px
      img
        width: 165px
    .l-penguin
      transform: rotateY(180deg)
      left: 10px
    .r-penguin
      right: 10px
    .close
      position: absolute
      top: 0
      right: 0
      font-size: 28px
      padding: 15px 25px
    .setting-block
      z-index: 999
.d-btn
  font-size: 20px
  margin: 10px
  border: $d-color solid 3px
  background-color: $l-blue
  min-width: 300px
  @include shadow-bottom
  line-height: 35px
  border-radius: 5px
  cursor: pointer
  z-index: 20
  outline: none
  &.blue-btn
    background-color: $d-color
    border: $l-blue solid 3px
    color: #E9F5FE
</style>
