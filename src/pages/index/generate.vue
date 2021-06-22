<template>
  <view class="wrap">
    <scroll-view  :scroll-y="true" class="genbox">
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

<style lang="scss" >
  .genbox{
    position: fixed;
    height: 100%;
    padding: 1em 0 7.5em;
    box-sizing: border-box;
    text-align: center;
    background-color:#ccc;
  }
  .uni-image{
    width:100%;
  }
</style>