<template>
	<view class="list">
		<view class="course_list">
			<!-- 嵌套view课程列表 -->
			<view class="course_item" v-for="course in courseList" :key="course.id">
				<view class="course_img">
					<image :src="course.cover" mode=""></image>
				</view>
				<view class="course_info">
					<view class="courses_tilte">
						{{course.title}}
					</view>
					<view class="course_desc">
						购买人数：{{course.buyCount}}
					</view>
					<view class="course_btn">
						<view class="course_attention">
							<button size="mini"
							style="color:#FF5A5F;border:0 solid #FFFFFF;background-color: #FFFFFF;" @click="deleteCourse(course.id)">删除课程</button>
							<button size="mini" 
							style="color:#F0AD4E;border:0 solid #FFFFFF;background-color: #FFFFFF;" 
							@click="courseInfo(course.id)">详情</button>	
						</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				//15195078422
				courseList:{},
				baseUrl : 'http://c3wjib.natappfree.cc'
			}
		},
		onLoad() {
			
			const userToken = uni.getStorageSync('token');
			 uni.request({
			 	url:this.baseUrl+'/api/teacher/getCourseList',
			 	header:{
			 		"token":userToken
			 	},
			 	method:'GET',
			 	success: (res) => {
					this.courseList = res.data.data
			 		// console.log(res.data.data)
			 	}
			 })
		},
		methods: {
			courseInfo(courseId){
				// console.log(courseId)
				uni.setStorageSync('courseId', courseId);
				uni.navigateTo({
					url:'../course/index',
					success: function(res) {
					    // console.log(courseId)
					  }
				})
			},
			deleteCourse(courseId){
				uni.request({
					url:this.baseUrl+'/api/eduCourse/removeCourseInfo/'+courseId,
					method:'DELETE',
					success: (res) => {
						console.log(res.data)
						//删除成功以后刷新列表
						const userToken = uni.getStorageSync('token');
						 uni.request({
						 	url:this.baseUrl+'/api/teacher/getCourseList',
						 	header:{
						 		"token":userToken
						 	},
						 	method:'GET',
						 	success: (res) => {
								this.courseList = res.data.data
						 		// console.log(res.data.data)
						 	}
						 })
					}
				})
			}
		}
	}
</script>

<style lang="scss">	

.course_list{
	padding: 10rpx;
	.course_item{
		padding: 10rpx 0;
		display: flex;
		border-bottom: 1rpx solid #2C405A;
		.course_img{
			flex: 1;
			padding: 10rpx;
			image{
				height: 200rpx;
				width: 200rpx;
			}
		}
		.course_info{
		flex: 2;
		padding: 0 10rpx;
		overflow: hidden;
		.courses_tilte{
			font-size: 30rpx;
			color: #3B4144;
			padding: 10rpx 0;
		}
		.course_desc{
			padding: 10rpx 0;
			font-size: 30rpx;
			
			text-overflow: ellipsis;
			overflow: hidden;
			white-space: nowrap;
		}
		
		.course_btn{
			padding: 10rpx 0;
			display: flex;
			justify-content: flex-end;
			.course_attention{
				color: #F0AD4E;
				// border: 1rpx solid #2C405A;
				padding: 10rpx;
			}
		}
	}
}
	
}
</style>
