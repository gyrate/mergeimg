<template>
	<view class="wrap">

		<view class="main" >
			<view class="img-item" v-for="(item,index) in imageList" :key="index">
				<image class="img" mode="widthFix" :src="item.src"></image>
				<uni-icons type="trash" size="30" @click="removeItem(index)"></uni-icons>
			</view>
			<view class="add"  @click="chooseImage">
				<view>
					<uni-icons type="plusempty" color="#007aff" size="40" ></uni-icons>
				</view>
				<view>
					<text>添加图片</text>
				</view>
			</view>
		</view>

		<view class="toolbar">
			<button type="primary"  :disabled="!canMerge" @click="goto">开始拼合</button>
		</view>

	</view>
</template>

<script>
  var sourceType = [
    ['camera'],
    ['album'],
    ['camera', 'album']
  ]

  var ctx = null

	export default {
		data() {
			return {
        title: 'choose/previewImage',
        imageList: [],
        sourceTypeIndex: 2,
        sourceType: ['拍照', '相册', '拍照或相册'],
        sizeTypeIndex: 2,
        sizeType: ['压缩', '原图', '压缩或原图'],
        countIndex: 8,
        count: [1, 2, 3, 4, 5, 6, 7, 8, 9],
        canvasWidth: 0,
        canvasHeight: 0,

			}
		},
		onLoad() {

		},
		computed:{
			canMerge(){
				return this.imageList.length >=1
			}
		},
		methods: {
      goto(){

        const query  = uni.createSelectorQuery();
        query.selectAll('.img-item').fields({size:true, rect:true},res=>{

          res.forEach((item, index) => {
			this.imageList[index].width = Math.floor(item.width)
			this.imageList[index].height = Math.floor(item.height)
          })

          const app = getApp()
          app.globalData.imgList = [...this.imageList]

          uni.navigateTo({
            url: '../index/merge',
            animationType: 'pop-in',
            animationDuration: 200
          })
				}).exec()

	  },

      removeItem(index){
        this.imageList.splice(index, 1)
	  },

      chooseImage: async function() {
        // #ifdef APP-PLUS
        // TODO 选择相机或相册时 需要弹出actionsheet，目前无法获得是相机还是相册，在失败回调中处理
        if (this.sourceTypeIndex !== 2) {
          let status = await this.checkPermission();
          if (status !== 1) {
            return;
          }
        }
        // #endif
        if (this.imageList.length === 9) {
          let isContinue = await this.isFullImg();
          console.log("是否继续?", isContinue);
          if (!isContinue) {
            return;
          }
        }
        var t = this
        uni.chooseImage({
          sourceType: sourceType[this.sourceTypeIndex],
          sizeType: this.sizeType[this.sizeTypeIndex],
          count: this.imageList.length + this.count[this.countIndex] > 9 ? 9 - this.imageList.length : this.count[this.countIndex],
          success: (res) => {
						const arr = res.tempFilePaths.map(item=>{
						  return {
                src: item
							}
						})
            t.imageList = t.imageList.concat(arr);
          },
          fail: (err) => {
            console.log("err: ",err);
            // #ifdef APP-PLUS
            if (err['code'] && err.code !== 0 && this.sourceTypeIndex === 2) {
              this.checkPermission(err.code);
            }
            // #endif
            // #ifdef MP
            if(err.errMsg.indexOf('cancel') !== '-1'){
              return;
            }
            uni.getSetting({
              success: (res) => {
                let authStatus = false;
                switch (this.sourceTypeIndex) {
                  case 0:
                    authStatus = res.authSetting['scope.camera'];
                    break;
                  case 1:
                    authStatus = res.authSetting['scope.album'];
                    break;
                  case 2:
                    authStatus = res.authSetting['scope.album'] && res.authSetting['scope.camera'];
                    break;
                  default:
                    break;
                }
                if (!authStatus) {
                  uni.showModal({
                    title: '授权失败',
                    content: 'Hello uni-app需要从您的相机或相册获取图片，请在设置界面打开相关权限',
                    success: (res) => {
                      if (res.confirm) {
                        uni.openSetting()
                      }
                    }
                  })
                }
              }
            })
            // #endif
          }
        })
      },

		}
	}
</script>

<style lang="scss" type="text/scss">

	.main{

		.img-item{
			position: relative;
			width: 100%;
			background-color: #fff;
			font-size: 0;
			border-top:1px dashed #ccc;

			&:first-child{
				border-top:0 none;
			}

			uni-image{
				width:100%;
			}
			.uni-icons{
				position:absolute;
				top: 20rpx;
				right: 20rpx;
			}
		}

		.add{
			padding: 1em;
			margin: 1em;
			background-color: #f0f0f0;
			color: $uni-color-primary;
		}
	}


</style>
