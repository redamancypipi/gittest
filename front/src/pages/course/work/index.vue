<template>
	<view >
		<view class="work-works">
			<text>已发布的作业</text>
			<!-- 添加作业 -->
			<button class="work-add" @click="open">+</button>
			<!-- 弹出框 -->
			<uni-popup ref="popup" type="dialog" style="width: 100%">
				<view style="width: 300px; height: 350px;background: #fff;">
					<view style="margin-top:20px;margin-left:10px;"><text style="margin-top:20px;display: flex;justify-content: center; ">作业列表:布置作业</text></view>
					<view style="margin-top:10px;margin-left:10px;">
						<text >作业标题：</text>
						<input style="margin-top:10px;" placeholder="默写" v-model="task.title"/>
					</view>
					<view style="margin-top:10px;margin-left:10px;">
						<text >作业内容：</text>
						<input style="margin-top:10px;" placeholder="如：默写《静夜诗》" v-model="task.content"/>
					</view>
					<view style="margin-top:10px;margin-left:10px;">
						<text >提交截至时间：</text>
                    	<view style="margin-top:10px;">
                      		<picker mode="date" :value="date" :start="startDate" :end="endDate" @change="bindDateChange" v-model="task.deadline">
                        	<view class="uni-input" >{{date}}</view>
                    		</picker>
                		</view>
					</view>
					<button class="work-sure" @click="confirm">确定</button>
					<button class="work-close" @click="close">取消</button>
				</view>
			</uni-popup>
		</view>
		<!--作业列表-->
		<view class="recommend_wrap">
			<view class="recommend_item" v-for="task in taskList" :key="task.id">
			  <view style="margin-top:8px;font-size:13px">作业标题：{{task.title}}</view>
			  <view style="margin-top:8px ;font-size:13px">作业内容: {{task.content}}</view>
			  <view style="margin-top:8px ;font-size:13px">提交截至时间：{{task.deadline|showTime}}</view>
			</view>
      	</view>
		
	</view>
</template>

<script>
import uniPopup from '@/components/uni-popup/uni-popup.vue'
import uniPopupMessage from '@/components/uni-popup/uni-popup-message.vue'
import uniPopupDialog from '@/components/uni-popup/uni-popup-dialog.vue'
import  {formatTime} from '@/utils/util.js'
export default {
		components: {
        uniPopup,
        uniPopupMessage,
        uniPopupDialog
    },
	data() {
			const currentDate = this.getDate({
            format: true
        })
        return {
            date: currentDate,
			baseUrl : 'http://c3wjib.natappfree.cc',//15195078422
			taskList:'',//后端的作业列表
			task:{
				deadline:'',
				title:'',
				content:''
			}//作业内容
        }
	},
	computed: {
        startDate() {
            return this.getDate('start');
        },
        endDate() {
            return this.getDate('end');
        }
    },
	filters: {
	    showTime(value) {
	        const date = new Date(value)
	        return formatTime(date,'yyyy-MM-dd hh:mm:ss');
	      }
	  },
	mounted() {
		const courseId = uni.getStorageSync('courseId');
		uni.request({
			url:this.baseUrl+'/api/teacher/getTaskList/'+courseId,
			method:'GET',
			success: (res) => {	
				this.taskList = res.data.data
				console.log(this.taskList)
			}
		})
	},
	methods: {
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
			uni.request({
				url:this.baseUrl+'/api/teacher/postTask/'+courseId,
				method:'POST',
				header:{
					'token':userToken
				},
				data:{
					courseId:courseId,
					content:this.task.content,
					title:this.task.title,
					deadline:this.task.deadline
				},
				success: (res) => {
					console.log(res.data)
					console.log(this.task)
					//发布作业以后刷新列表
					uni.request({
						url:this.baseUrl+'/api/teacher/getTaskList/'+courseId,
						method:'GET',
						success: (res) => {	
							this.taskList = res.data.data
							console.log(this.taskList)
						}
					})
				}
			})
		  // TODO 做一些其他的事情，手动执行 done 才会关闭对话框
		  // ...
		  	this.$refs.popup.close()
		  	// done()
	  	},
	  	bindDateChange: function(e) {
            this.date = e.target.value
			// console.log(this.date)
			this.task.deadline = this.date
		},
		getDate(type) {
            const date = new Date();
            let year = date.getFullYear();
            let month = date.getMonth() + 1;
            let day = date.getDate();

            if (type === 'start') {
                year = year - 60;
            } else if (type === 'end') {
                year = year + 2;
            }
            month = month > 9 ? month : '0' + month;;
            day = day > 9 ? day : '0' + day;
            return `${year}-${month}-${day}`;
        },
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
	.work-sure{
		width:70px;
        height:40px;
        margin-right: 10px;
        margin-top:30px;
        font-size: 14px;
        background-color: blue;
		color:white;
        display: flex;
        justify-content: center;
        align-items: center;
	}
	.work-close{
		width:70px;
        height:40px;
        margin-right: 100px;
        margin-top:-40px;
        font-size: 14px;
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
