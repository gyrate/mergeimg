<template>
  <view class="wrap">

      <view class="main">
        <canvas canvas-id="canvas" id="canvas" class="canvas" :style="{height: canvasHeight+'px'}"></canvas>
      </view>
      <movable-area :style="{height: canvasHeight+'px'}" >
        <movable-view :x="100" :y="200" direction="all" @change="onChange" v-show="textVisible">
          <textarea auto-height v-model="text" style="color:red;font-size:40px;"/>
        </movable-view>
      </movable-area>

      <view class="botbar uni-flex uni-row">
        <uni-view  class="tool">
          <image mode="aspectFit"  src="/static/font.png" @click="toggleText"></image>
        </uni-view>
        <uni-view  class="text" style="width: 100px;" @click="generate">生成图片</uni-view>
      </view>
  </view>
</template>

<script>
  export default {
    name: 'merge',
    components: {},
    data() {
      return {
        canvasWidth: null,
        canvasHeight: 0,
        ctx: null,
        flag: 0,
        text: '文本',
        textVisible: false,

        cur: {
          x: 100,
          y: 200
        }
      }
    },
    onReady: function () {
      this.$nextTick(() => {
        uni.createSelectorQuery().select('.canvas').boundingClientRect(data => {
          this.canvasWidth = data.width
          this.ctx = uni.createCanvasContext('canvas')

          this.drawImg()
          this.ctx.draw()
        }).exec()
      })
    },
    mounted() {

    },
    methods: {

      toggleText(){
        this.textVisible = !this.textVisible
      },

      onChange: function (e) {
        const {x, y} = e.detail
        this.cur.x = x;
        this.cur.y = y
      },

      async drawImg() {
        const app = getApp()
        const imgList = app.globalData.imgList
        if (imgList.length <= 0) {
          return
        }

        this.canvasHeight = imgList.reduce((a, b) => {
          return a + b.height
        }, 0)
        console.log(`this.canvasHeight ${this.canvasHeight}`)

        var flag = 0
        for (let i = 0; i < imgList.length; i++) {
          const {width, height, src} = imgList[i]

          this.ctx.drawImage(src, 0, flag, width, height)
          flag += height
          console.log(`width:${width},height:${height},flag:${flag}`)
        }
        // this.ctx.draw()
      },

      // loadImage(src) {
      //   return new Promise(resolve => {
          // const img = new Image()
          // img.src = src
          // img.onload = function(){
          //   console.log(`${img.width},${img.height}`)
          // resolve(src)
          // }
      //   })
      // },

      drawText() {
        if (this.textVisible) {
          const ctx = this.ctx
          ctx.font = "40px Arial";
          ctx.fillStyle = "red";
          // ctx.textBaseline = "top";
          ctx.fillText(this.text, this.cur.x +1, this.cur.y + 42);
          this.textVisible = false
        }
      },

      generate() {

        this.drawImg()
        this.drawText()
        this.ctx.draw()

        //画布生成图片
        uni.canvasToTempFilePath({
          x: 0,
          y: 0,
          width: this.canvasWidth,
          height: this.canvasHeight,
          canvasId: 'canvas',
          success: function (res) {
            console.log(res.tempFilePath)
            //保存最终结果
            getApp().globalData.output = res.tempFilePath

            uni.navigateTo({
              url: '../index/generate',
              animationType: 'pop-in',
              animationDuration: 200
            })
          }
        })
      }

    }
  }
</script>

<style lang="scss" scoped>

  uni-movable-area  {
    position: absolute;
    top:0;
    left:0;
    width: 100%;
    background-color: transparent;
    overflow: hidden;
    padding-bottom: 0;
  }
  uni-movable-view {
    display: -webkit-box;
    display: flex;
    -webkit-box-align: center;
    align-items: center;
    -webkit-box-pack: center;
    justify-content: center;
    height:52px;
    width: 150px;
    color: #333;

    uni-textarea{
      border: 1px solid #007aff;
    }
  }

  .canvas{
    width: 750rpx;
    margin: auto;
    background-color: #ccc;
  }
  .botbar{
    position: fixed;
    bottom:0;
    left:0;
    width: 100%;
    padding: 0.6em 1em  ;
    box-sizing: border-box;
    text-align: right ;
    background-color:$uni-color-primary;
    color: $uni-text-color-inverse;

    .tool{
      flex:1;
      text-align: left;
      color: #fff;

      uni-image{
        width: 20px;
        height: 20px;
        vertical-align: middle;
      }
    }
  }
</style>