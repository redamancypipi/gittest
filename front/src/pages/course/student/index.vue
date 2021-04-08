<template>
	
	<uni-list>
	    <uni-list :border="true">
	        
	        <uni-list-chat v-for="student in studentList" :key="student.id" :title="student.name" :avatar="student.avatar" ></uni-list-chat>
	        
	        </uni-list-chat>
	    </uni-list>
	</uni-list>

</template>

<script>
	export default {
		data() {
			return {
				baseUrl:'http://c3wjib.natappfree.cc',
				studentList:''
			}
		},
		mounted() {
			//15195078422
			const courseId = uni.getStorageSync('courseId');
			//获取评论列表
			uni.request({
				url:this.baseUrl+'/api/teacher/getMemberList/'+courseId,
				method:'GET',
				success: (res) => {	
					this.studentList = res.data.data
					console.log(this.studentList)
				}
			})
		},
		methods: {

		}
	}
</script>

<style lang="scss">
	
.chat-custom-right {
    flex: 1;
    /* #ifndef APP-NVUE */
    display: flex;
    /* #endif */
    flex-direction: column;
    justify-content: space-between;
    align-items: flex-end;
}

.chat-custom-text {
    font-size: 12px;
    color: #999;
}
	
</style>
