<template>
	<view class="my-picture">
		<image :src="teacherInfo.avatar" mode="aspectFit" 
		style="height: 80px;width: 80px;border-radius: 50%;margin-left: 40%;" ></image>
		<view calss="content">
			<uni-list>
				<uni-list :border="true">
					<uni-list-chat :avatar-circle="true" title="教师姓名" avatar="/static/icon/用户.png">
						<input type="text" :value="teacherInfo.name"/></uni-list-chat>		   
					<uni-list-chat :avatar-circle="true" title="教师id" avatar="/static/icon/领带.png" :note="teacherInfo.id"></uni-list-chat>		 
					<uni-list-chat :avatar-circle="true" title="手机号" avatar="/static/icon/id.png" 
					:note="teacherInfo.tel" badge-positon="left" ></uni-list-chat>
					<uni-list-chat :avatar-circle="true" title="教师简介" avatar="/static/icon/信息.png"  badge-positon="left" >				
					</uni-list-chat>
						<input type="text" :value="teacherInfo.career" />
					</uni-list-chat>
				</uni-list>
			</uni-list>
		
			<button class="btn1" size="mini" style="margin-left: 35%;background-color: #FFFFFF;color: #F0AD4E;" @click="createSubject()">新建课程</button>
			<button class="btn2" size="mini" style="margin-left: 35%;background-color: #FFFFFF;color: #F0AD4E;" @click="submitInfo()">提交修改</button>
		
			
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				teacherInfo:'',
				baseUrl : 'http://c3wjib.natappfree.cc',
			}
		},
		mounted() {
			//教师信息
			const userToken = uni.getStorageSync('token')
			uni.request({
				url:this.baseUrl+'/api/eduTeacher/auth/getTeacherInfo',
				method:'GET',
				header:{
					token:userToken
				},
				success: (res) => {
					console.log(res.data.data.item)
					this.teacherInfo = res.data.data.item
				}
			})
		},
		methods: {
			submitInfo(){
				//提交教师信息
				uni.request({
					url:this.baseUrl +'/api/eduTeacher/update',
					method:'POST',
					data:{
						id:this.teacherInfo.id,
						avatar:this.teacherInfo.avatar,
						name:this.teacherInfo.name,
						career:this.teacherInfo.career,
						tel:this.teacherInfo.tel
					},
					success: (res) => {
						console.log(res.data)
					}
				})
			},
			//新建课程
			createSubject(){
				uni.navigateTo({
					url:'../index2/index'
				})
			}
		}
	}
</script>

<style lang="scss">

page{ 
 height: 100%; 
} 

	
.content{

width: 1000px; 
height: 1000px; 
background-color: #ffffff;
}
.teacher_id{
	
}	

</style>
