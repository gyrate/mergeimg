<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		},
    globalData: {
		  //多张原始图片列表
      imgList: [],
      //最终生成图片的地址base64
      output: null
    }
	}
</script>

<style>
	/*每个页面公共css */
</style>
