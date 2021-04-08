<template>
	<view>
		<uni-steps :options="[{title: '填写课程信息'}, {title: '添加章节'},{title: '发布课程'}]" :active="1">	
		</uni-steps>
		<!-- 添加章节 -->
		<view class="uni-flex uni-row">
			<button size="mini" style="margin-left: 290rpx;background-color: #F0AD4E;" @click="open">添加章节</button>
			<!-- 章节弹窗 -->
			<uni-popup ref="popup" type="dialog">
				<uni-popup-dialog type="info" title="添加章节"
				
				:duration="2000" :before-close="true"
				 mode="input" placeholder="请输入章节题目"
				 @close="close" @confirm="confirm">
				 </uni-popup-dialog>
			</uni-popup>
		</view>
		<button size="mini" style="margin-left: 300rpx;background-color: #F0AD4E;" @click="createSubject()">下一步</button>
	</view>
</template>
<script>
	import uniPopup from '@/components/uni-popup/uni-popup.vue'
	import uniPopupMessage from '@/components/uni-popup/uni-popup-message.vue'
	import uniPopupDialog from '@/components/uni-popup/uni-popup-dialog.vue'
	import uniSteps from '@/components/uni-steps/uni-steps.vue'
	export default {
	    components: {
			uniSteps,
			uniPopup,
			uniPopupMessage,
			uniPopupDialog
		},
		data() {
			return {
				chapterInfo:{
					courseId:'',
					sort:'',
					title:''
				 },//新添加的章节信息
				 chapterSort:'',//新增章节排序
				 baseUrl : 'http://c3wjib.natappfree.cc'
			}
		},
		methods: {
			createSubject(){
				//提交信息并跳转到下一步
				uni.navigateTo({
					url:'../publish/index'
				})
			},
			
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
					  const courseId = uni.getStorageSync('newcourseId');
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
					  
					  done()
			}
		}
	}
	
</script>

<style>
	
</style>
