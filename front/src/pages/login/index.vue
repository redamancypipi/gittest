<template>
	<view >
		<image src="/static/logo.png" mode="aspectFit" style="height:20px;height:40px;margin-top:20px;"></image>
		<view class="container"> 	
			<view class="login-from"> 			
				
				<view class="inputView"> 
					<image class="nameImage" src="/static/icon/telicon.png"></image> 
					<label class="loginLab">账号</label> 
					<input class="inputText" placeholder="请输入账号" v-model="userInfo.tel"/> 
				</view> 
				<view class="line"></view> 			
				
					<view class="inputView"> 
						<image class="keyImage" src="/static/icon/pwdicon.png"></image> 
						<label class="loginLab">密码</label> 
						<input class="inputText" password="true" placeholder="请输入密码" v-model="userInfo.password"/> 
					</view> 		
				
				<view class="loginBtnView"> 
					<button class="loginBtn" type="primary" :size="primarySize" :loading="loading" 
					:plain="plain" :disabled="disabled" @click="login()">登录</button> 
				</view> 
			</view> 
		</view>
	</view>
</template>

<script>
	export default {		
		data() {
			return {
				
				userInfo:{
					tel:'',
					password:''
				},
				
			
			}
		},
		onLoad() {

		},
		methods: {
            login(){
				const baseUrl = 'http://c3wjib.natappfree.cc';
				uni.request({
					url: baseUrl + '/api/eduTeacher/login', 
					method:'POST',
					data: {
						  tel:this.userInfo.tel,
						  password:this.userInfo.password
					},
					success: (res) => {
						console.log(res.data)
						if(res.data.code == 200){
							//保存token
							uni.setStorageSync('token', res.data.data.token);
							
							uni.switchTab({
								url: '../index/index',
							})
							
						}
					}
				})
			}
		}
	}
</script>

<style lang="scss">
	
page{ 
 height: 100%; 
} 
 
.container { 
 height: 100%; 
 display: flex; 
 flex-direction: column; 
 padding: 0; 
 box-sizing: border-box; 
 /* background-color: rgb(156, 23, 78) */
} 
 
/*登录图片*/
.login-icon{ 
 flex: none; 
} 
 
.login-img{ 
 width: 750rpx;
} 
 
/*表单内容*/
.login-from { 
 margin-top: 20px; 
 flex: auto; 
 height:100%; 
} 
 
.inputView { 
 /* background-color: #fff;  */
 line-height: 45px; 
 border-radius:20px;
  border:1px solid #999999;
} 
 
/*输入框*/
.nameImage, .keyImage { 
 margin-left: 22px; 
 width: 18px; 
 height: 16px
} 
 
.loginLab { 
 margin: 15px 15px 15px 10px; 
 color: #545454; 
 font-size: 14px
} 
 
.inputText { 
 flex: block; 
 float: right; 
 text-align: right; 
 margin-right: 22px; 
 margin-top: 11px;
 color: #cccccc; 
 font-size: 14px
} 
.line { 
 margin-top: 8px; 
} 
 
/*按钮*/
.loginBtnView { 
 width: 100%; 
 height: auto; 
 /* background-color:#DCDCDC;  */
 margin-top: 0px; 
 margin-bottom: 0px; 
 padding-bottom: 0px; 
} 
 
.loginBtn { 
 width: 90%; 
 margin-top: 40px; 
 border-radius:10px;
}
</style>
