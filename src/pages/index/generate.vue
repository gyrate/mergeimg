<template>
  <view class="wrap">
    <scroll-view  :scroll-y="true" style="background-color:#ccc;text-align: center;height: 80%;">
      <image mode="widthFix" :src="imgSrc"  ></image>
    </scroll-view>

    <view class="toolbar uni-row">
      <button type="primary" @click="save">保存到相册</button>
    </view>
  </view>
</template>

<script>
  export default {
    name: 'generate',
    components: {},
    data() {
      return {
        imgSrc: null
      }
    },
    onReady(){
      const app = getApp()
      this.imgSrc = app.globalData.output
    },
    mounted() {
    },
    methods: {
      save(){
        uni.saveImageToPhotosAlbum({
        	filePath: this.imgSrc,
        	success:(res)=>{
            uni.showToast({
              title: '图片保存成功',
              icon: 'none'
            })
        	},
        	fail:(res)=>{
            uni.showToast({
              title: '图片保存失败',
              icon: 'none'
            })
        	},
          complete:(res)=>{
        	  //
        	}
        })
      }
    }
  }
</script>

<style lang="scss" type="text/scss">
  .uni-image{
    width:100%;
  }
</style>