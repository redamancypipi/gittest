<template>
	<view>
		<page-head title="view"></page-head>
		<view class="uni-padding-wrap uni-common-mt">
			<view class="uni-flex uni-row">
				<view class="classicon">
					<image :src="courseInfo.cover" mode="" style="height: 300rpx;"></image>
				</view>
				
				<view class="uni-flex uni-column" style="-webkit-flex: 1;flex: 1;-webkit-justify-content: space-between;justify-content: space-between;">
					<!-- 章节列表 -->
					<view class="text" v-for="(chapter,index) in chapterList" :key="index" style="height: 100rpx;
					text-align: left;padding-left: 20rpx;padding-top: 10rpx;">
						第{{index+1}}章&nbsp;{{chapter.title}}
						<!-- 小节列表 -->
						<!-- <view class="text" v-for="(video,index1) in chapter.children" :key="index1" style="height: 50rpx;
						text-align: left;padding-left: 10rpx;padding-top: 10rpx;">
						第{{index1+1}}节&nbsp;{{video.title}}
						</view> -->
					</view>
					
					<!-- 添加章节弹窗 -->
					<view class="uni-flex uni-row">
						<button size="mini" style="margin-left: 300rpx;background-color: #F0AD4E;" @click="open">添加章节</button>
						<uni-popup ref="popup" type="dialog">
							<uni-popup-dialog type="info" title="添加章节"
							
							:duration="2000" :before-close="true"
							 mode="input" placeholder="请输入章节题目"
							 @close="close" @confirm="confirm">
							 </uni-popup-dialog>
						</uni-popup>
					</view>
				</view>
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
			chapterList:{},//获取到的章节列表
			chapterInfo:{
				courseId:'',
				sort:'',
				title:''
			 },//新添加的章节信息
			 chapterSort:'',//新增章节排序
			 courseInfo:'',//课程信息
			 baseUrl : 'http://c3wjib.natappfree.cc'
		}
	},
	mounted() {
		
		const courseId = uni.getStorageSync('courseId');
		console.log(courseId)
		// //15195078422
		uni.request({
			url:this.baseUrl+'/api/eduChapter/getChapterVideo/'+courseId,
			method:'GET',
			success: (res) => {
				this.chapterList = res.data.data.allVideos
				// console.log(this.chapterList)
				this.chapterSort = this.chapterList.length
				console.log(res.data)
				uni.request({
					url:this.baseUrl+'/api/eduCourse/getCourseInfo/'+courseId,
					method:'GET',
					success: (res) => {
						console.log(res.data)
						this.courseInfo = res.data.data
					}
				})
			}
		})
	},
	methods:{
	  open(){
		 this.$refs.popup.open()
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
		  const courseId = uni.getStorageSync('courseId');
		  // 输入框的值
		  console.log(value)
		  
		  // ...15195078422
		  uni.request({
		  	url:this.baseUrl+'/api/eduChapter/addChapter',
		  	method:'POST',
			data:{
				
				courseId:courseId,
				title:value,
				sort:this.chapterSort
			},
		  	success: (res) => {
		  		
		  		console.log(res.data)
		  	}
		  })
		  //添加章节以后刷新页面
		  
		  console.log(courseId)
		  // //15195078422
		  uni.request({
		  	url:this.baseUrl+'/api/eduChapter/getChapterVideo/'+courseId,
		  	method:'GET',
		  	success: (res) => {
		  		this.chapterList = res.data.data.allVideos
		  		
		  		this.chapterSort = this.chapterList.length
		  		console.log(this.chapterList)
		  	}
		  })
		  done()
	  }
   }
}
</script>

<style>
	.flex-item {
		width: 33.3%;
		height: 200rpx;
		text-align: center;
		line-height: 200rpx;
	}

	.flex-item-V {
		width: 100%;
		height: 150rpx;
		text-align: center;
		line-height: 150rpx;
	}

	.text {
		margin: 15rpx 10rpx;
		padding: 0 20rpx;
		background-color: #ebebeb;
		height: 70rpx;
		line-height: 70rpx;
		text-align: center;
		color: #777;
		font-size: 26rpx;
	}

	.desc {
		/* text-indent: 40rpx; */
	}
	.flex-pc {
		display: flex;
		justify-content: center;
	}
</style>
