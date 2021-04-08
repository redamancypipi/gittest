<template>
	<view class="list">
		<view class="course_list">
			<view class="course_item">
				<view class="course_img">
					<image :src="teacherInfo.avatar"></image>
				</view>
				<view class="course_info">
					<view class="courses_tilte">
						{{teacherInfo.name}}
					</view>
					<view class="course_desc">
						<input type="text" placeholder="请输入内容" v-model="Comment.content"/>
					</view>
					<view class="course_btn">
						<view class="course_attention">
							<button style="width: 100rpx;height: 50rpx;
							font-size: 21rpx;padding: 5rpx;background-color: #F0AD4E;"@click="submitparentComm()">发表评论</button>
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 评论列表 -->
		<uni-list>
		    <uni-list :border="true">
				<view class="comment" v-for="(comment,index1) in commList" :key="index1">
					<!-- 父评论列表 -->
					<uni-list-chat :avatar-circle="true" 
					:title="comment.nickname" 
					:avatar="comment.avatar" 
					:note="comment.content"  >
					<button style="margin-top:50rpx;width: 60rpx;
					height: 50rpx;font-size: 20rpx;padding: 5rpx;background-color: #F0AD4E;"
					@click="submitComm(index1)">回复</button>									
					</uni-list-chat>
					<view v-if="comment.status == 1" class="childComm">
					<input  type="text" placeholder="请输入内容" v-model="CommentChild.content"/>
					<button style="width: 100rpx;height: 50rpx;
					font-size: 21rpx;padding: 5rpx;background-color: #F0AD4E;
					margin-right: 20rpx;margin-bottom: 10rpx;" @click="submitchildComm(index1,comment.id,comment.nickname)">发表评论</button>
					</view>
					<!-- 子评论列表 -->
					<view style="margin-left:20px;" v-for="(commentChild,index2) in comment.children " :key="index2">
						<uni-list-chat :avatar-circle="true" 
						:title="commentChild.nickname"  
						:avatar="commentChild.avatar" 
						:note="comment.content" >
						<text style="margin-right:142px;margin-top:3px;">回复：</text>
						<text style="margin-right:70px;margin-top:-20px;font-size:17px;color:grey">{{commentChild.realName}}</text>
						<button style="margin-right:3rpx;width: 60rpx;
						height: 50rpx;font-size: 20rpx;padding: 5rpx;background-color: #F0AD4E;"
						@click="submitComm1(index1,index2)">回复</button>
						</uni-list-chat>
						<!-- 弹出回复框 -->
						<view v-if="commentChild.status == 1" class="childComm">
						<input  type="text" placeholder="请输入内容" v-model="CommentChild.content"/>
						<button style="width: 100rpx;height: 50rpx;
						font-size: 21rpx;padding: 5rpx;background-color: #F0AD4E;
						margin-right: 20rpx;margin-bottom: 10rpx;" @click="submitchildComm(index2,commentChild.id,commentChild.nickname)">发表评论</button>
						</view>
					</view>
					
					
				</view>
		    </uni-list>
		</uni-list>
	</view>

</template>
<script>
	export default {
		data() {
			return {
				 comm:'',
				 commList:{},  //评论列表
				 teacherInfo:'',
				 Comment:{
					 content:'',
					 courseId:'',
					 nickname:'',
					 id:'',
					 pid:'',
					 avatar:'',
					 memberId:'',
			   },//评论内容
			   CommentChild: {
				 content: "",
				 courseId: "",
				 pid: "",
				 avatar: "",
				 nickname: "",
				 memberId: "",
				 realName: "",
			   },//子评论内容
			   baseUrl : 'http://c3wjib.natappfree.cc'
			}
		},
		mounted() {
			//15195078422
			const courseId = uni.getStorageSync('courseId');
			//获取评论列表
			uni.request({
				url:this.baseUrl+'/api/eduComment/getCommentList/'+courseId,
				method:'GET',
				success: (res) => {
					console.log(res.data)
					this.commList = res.data.data
				}
			})
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
			submitComm(index){
				//点击弹出回复框
				// this.comm='1';
				this.commList[index].status = 1;
				
			},
			submitComm1(index1,index2){
				//点击弹出回复框
				// this.comm='1';
				this.CommentChild.content = "";
				this.commList[index1].children[index2].status = 1;
				
			},
			submitchildComm(index,pid,realName){
				//回复评论
				this.commList[index].status = 0;
				const courseId = uni.getStorageSync('courseId');
				console.log(pid)
				uni.request({
					url:this.baseUrl+'/api/eduComment/postComment',
					method:'POST',
					data:{
						pid:pid,
						courseId:courseId,
						content:this.CommentChild.content,
						memberId:this.teacherInfo.id,
						nickname:this.teacherInfo.name,
						avatar:this.teacherInfo.avatar,
						realName:realName
					},
					success: (res) => {
						console.log(res.data)
						this.CommentChild.content = ''
						//发表评论以后刷新
						uni.request({
							url:this.baseUrl+'/api/eduComment/getCommentList/'+courseId,
							method:'GET',
							success: (res) => {
								console.log(res.data)
								this.commList = res.data.data
							}
						})
					}				
				})
			},
			submitparentComm(){
				const courseId = uni.getStorageSync('courseId');
				//教师发表评论
				uni.request({
					url:this.baseUrl+'/api/eduComment/postComment',
					method:'POST',
					data:{
						pid:'0',
						courseId:courseId,
						content:this.Comment.content,
						memberId:this.teacherInfo.id,
						nickname:this.teacherInfo.name,
						avatar:this.teacherInfo.avatar
					},
					success: (res) => {
						console.log(res.data)
						this.Comment.content = ''
						//发表评论以后刷新
						uni.request({
							url:this.baseUrl+'/api/eduComment/getCommentList/'+courseId,
							method:'GET',
							success: (res) => {
								console.log(res.data)
								this.commList = res.data.data
							}
						})
					}				
				})
				// this.Comment = ''
				
			}
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
				height: 100rpx;
				width: 100rpx;
				border-radius: 50%;
			}
		}
		.course_info{
		flex: 2;
		padding: 0 10rpx;
		overflow: hidden;
		.courses_tilte{
			font-size: 40rpx;
			color: #3B4144;
			padding: 10rpx 0;
		}
		.course_desc{
			padding: 10rpx 0;
			font-size: 35rpx;
			
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
