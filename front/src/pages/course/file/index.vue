<template>
	<view >
		<view class="work-works">
			<text>已上传的课件</text>
			<button class="work-add" @click="open">+</button>
			
		</view>
		<!--可参考视频p29-->
		<view class="recommend_wrap">
        <view class="recommend_item" v-for="file in filelist" :key="file.id">
			<navigator :url="file.url">
				<view style="margin-top:8px;font-size:13px">文件名称：C语言学习大纲</view>
			</navigator>         
		    <view style="margin-top:8px ;font-size:13px">id: {{file.id}}</view>
		</view>
      	</view>
	</view>
</template>

<script>
import uniPopup from '@/components/uni-popup/uni-popup.vue'
import uniPopupMessage from '@/components/uni-popup/uni-popup-message.vue'
import uniPopupDialog from '@/components/uni-popup/uni-popup-dialog.vue'
	export default {
		components: {
        uniPopup,
        uniPopupMessage,
        uniPopupDialog
	},
	
		data() {
			return {
				baseUrl : 'http://c3wjib.natappfree.cc',//15195078422
				filelist:''//文件列表15195078422
			}
		},
		mounted() {
			const courseId = uni.getStorageSync('courseId');
			uni.request({
				url:this.baseUrl+'/api/teacher/getFileList/'+courseId,
				method:'GET',
				success: (res) => {	
					this.filelist = res.data.data
					console.log(res.data)
				}
			})
		},
		methods: {
			open(){
				const courseId = uni.getStorageSync('courseId');
				uni.chooseImage({
					success: (chooseImageRes) => {
						const tempFilePaths = chooseImageRes.tempFilePaths;
						uni.uploadFile({
							url: this.baseUrl+'/api/teacher/uploadFile/'+courseId, 
							filePath: tempFilePaths[0],
							name: 'file',
							formData: {
								'user': 'test'
							},
							success: (uploadFileRes) => {
								console.log(uploadFileRes.data);
							},
							success: (res) => {
								console.log(res.data)
								//更新文件列表
								const courseId = uni.getStorageSync('courseId');
								uni.request({
									url:this.baseUrl+'/api/teacher/getFileList/'+courseId,
									method:'GET',
									success: (res) => {	
										this.filelist = res.data.data
										console.log(res.data)
									}
								})
							}
						});
					}
				});
		 		// this.$refs.popup.open()
	  		},
	  /**
	   * 点击取消按钮触发
	   * @param {Object} done
	   */
	  close(done){
		  // TODO 做一些其他的事情，before-close 为true的情况下，手动执行 done 才会关闭对话框
		  // ...
		  done()
	  },
	  /**
	   * 点击确认按钮触发
	   * @param {Object} done
	   * @param {Object} value
	   */
	  confirm(done,value){
		  // 输入框的值
		  console.log(value)
		  // TODO 做一些其他的事情，手动执行 done 才会关闭对话框
		  // ...
		  done()
	  }
		}
	}
</script>

<style lang="scss">
	.work-works{
		margin-top:30px;
		margin-left:10px;
	}
	.work-add{
		width:45px;
        height:30px;
        margin-right: 10px;
        margin-top:-25px;
        font-size: 25px;
        color: blue;
        display: flex;
        justify-content: center;
        align-items: center;
	}
	.work-content{
		width:300px;
		height:90px;
		border:1px solid rgb(243, 242, 242);
		background:rgb(243, 242, 242);
		margin-top:30px;
		margin-left:10px
	}
	.recommend_wrap{
        display:flex;
        flex-wrap:wrap;
        .recommend_item{
            width:300px;
            margin-top:20px;
            margin-left:10px;
            border:1px solid rgb(243, 242, 242);
		    background:rgb(243, 242, 242);

        }
    }
</style>
