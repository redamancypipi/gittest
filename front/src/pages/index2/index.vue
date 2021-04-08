<template>
	<view>
		<uni-steps :options="[{title: '填写课程信息'}, {title: '添加章节'},{title: '发布课程'}]" :active="0">	
		</uni-steps>
		<!-- 输入课程信息 -->
		<view class="subject_title" decoding="true">
			<text space="nbsp">课程名  :</text>
			<input type="text" placeholder="请输入课程名" v-model="CourseInfo.title"/>
		</view>
		<view class="subject_title" >
			<text space="nbsp">讲师ID  :</text>
			<input type="text" placeholder="请输入您的讲师ID" v-model="CourseInfo.teacherId"/>
		</view>
		<view class="subject_title" >
			<text space="nbsp">课程描述  :</text>
			<input type="text" placeholder="请输入课程描述" v-model="CourseInfo.desc"/>
		</view>
		<view class="subject_title" >
			<text space="nbsp">课程价格  :</text>
			<input type="text" placeholder="请输入课程价格" v-model="CourseInfo.price"/>
		</view>
		<view class="subject_title" >
			<text space="nbsp">课程一级分类  :</text>
			<input type="text" placeholder="请输入一级分类的ID值" v-model="CourseInfo.subjectParentId"/>
		</view>
		<view class="subject_title" >	
			<text space="nbsp">课程二级分类  :</text>			
			<input type="text" placeholder="请输入二级分类的ID值" v-model="CourseInfo.subjectId"/>
		</view>
		<view class="subject_title" >
			<text space="nbsp">课程封面  :</text>			
			<button size="mini" style="color: #F0AD4E;background-color: #FFFFFF;" @click="submitCover">点击上传封面</button>
		</view>
		
		<button size="mini" style="margin-left: 300rpx;background-color: #F0AD4E;margin-top: 5%;" @click="createSubject()">下一步</button>
		<view class="border--left">
			<view v-for="subject in subjectclass" decoding="true">
			{{subject.name}}&nbsp;&nbsp;&nbsp;ID为：
			{{subject.id}}
			</view>
		</view>
		<button size="mini" style="margin-left: 80rpx;background-color: #F0AD4E;" @click="subjectLevelOneChanged(CourseInfo.subjectParentId)">输入一级分类以后点此查询二级分类ID</button>
	
	 <view class="border--left">
	 	<view v-for="subject in subjectChild" decoding="true">
	 	{{subject.name}}&nbsp;&nbsp;&nbsp;ID为：
	 	{{subject.id}}
	 	</view>
	 </view>
		 
		
	
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
				baseUrl:'http://c3wjib.natappfree.cc',
				CourseInfo:{
					teacherId:'',
					title:'',
					desc:'',
					price:'',
					subjectParentId:'',
					subjectId:''
				},
				subjectclass:'',//课程分类
				subjectChild:'',//子分类
			}
		},
		mounted() {
			//查询分类
			const userToken = uni.getStorageSync('token')
			uni.request({
				url:this.baseUrl+'/api/eduSubject/getSubject/list',
				method:'GET',
				header:{
					'token':userToken
				},
				success: (res) => {
					this.subjectclass= res.data.data
					console.log(this.subjectclass)
				}
			})
		},
		methods: {
			subjectLevelOneChanged(value) {
			            //value就是一级分类id值
			            //遍历所有的分类，包含一级和二级
			            for(var i=0;i<this.subjectclass.length;i++) {
			                //每个一级分类
			                var oneSubject = this.subjectclass[i]
			                //判断：所有一级分类id 和 点击一级分类id是否一样
			                if(value === oneSubject.id) {
			                    //从一级分类获取里面所有的二级分类
			                    this.subjectChild = oneSubject.children
			                    //把二级分类id值清空
			                    this.CourseInfo.subjectId = ''
			                }
			            }
			        },
					
					
			open(){
					 	this.$refs.popup.open()
				},
			/**
			 * 点击取消按钮触发
			 * @param {Object} done
			 */
				close(){
					  
						  this.$refs.popup.close()
				},
			/**
			 * 点击确认按钮触发
			 * @param {Object} done
			 * @param {Object} value
			 */
				confirm(done,value){
					  // 输入框的值
					  	console.log(this.task)
						const courseId = uni.getStorageSync('courseId');
						const userToken = uni.getStorageSync('token');
						
						
					  // TODO 做一些其他的事情，手动执行 done 才会关闭对话框
					  // ...
					  	this.$refs.popup.close()
					  	// done()
				},
			//上传封面
			submitCover(){
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
							}
							
						});
					}
				});
				
		},
			
			createSubject(){
				//提交信息并跳转到下一步
				uni.request({
					url:this.baseUrl+'/api/eduCourse/addCourseInfo',
					data:{
						title:this.CourseInfo.title,
						teacherId:this.CourseInfo.teacherId,
						description:this.CourseInfo.desc,
						price:this.CourseInfo.price,
						subjectId:this.CourseInfo.subjectId,
						subjectParentId:this.CourseInfo.subjectParentId,
						cover:'http://minicourse1001.oss-cn-beijing.aliyuncs.com/2021/01/17/d5f31bd4bc1141019b77896ba54b0edae2f3c7bfab518109438f5613ff0e502f.jpeg'
					},
					method:'POST',
					success: (res) => {
						uni.setStorageSync('newcourseId',res.data.data.courseId)
						console.log(res.data.data.courseId)
						uni.navigateTo({
							url:'../home/index'
						})
					}
				})
				
			}
		}
	}
	
</script>

<style>
	.subject_title{
		display: flex;
		flex-wrap: nowrap;
		border: 1rpx solid #2C405A;
		height: 80rpx;
		margin-top: 5%;
		margin-left:5% ;
		width: 90%;
		border-radius: 8%;
	}
</style>
