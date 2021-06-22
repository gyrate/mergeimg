<template>
  <view class="wrap">

      <view class="main">
        <canvas canvas-id="canvas" id="canvas" class="canvas" :style="{height: canvasHeight+'px'}"></canvas>
      </view>

      <view class="botbar uni-flex uni-row">

        <uni-view  class="tool">
          <image mode="aspectFit"   src="/static/font.png"></image>
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
        count: 0,
        flag: 0
      }
    },
    onReady: function () {
      this.$nextTick(() => {
        uni.createSelectorQuery().select('.canvas').boundingClientRect(data => {
          this.canvasWidth = data.width
          this.ctx = uni.createCanvasContext('canvas')

          this.drawImg()
        }).exec()
      })
    },
    mounted() {

    },
    methods: {
      async drawImg(){
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
        this.ctx.draw()
      },

      loadImage(src){
        return new Promise(resolve=>{
          // const img = new Image()
          // img.src = src
          // img.onload = function(){
          //   console.log(`${img.width},${img.height}`)
            resolve(src)
          // }
        })
      },

      generate(){
        var t = this
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

<style lang="scss" >

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