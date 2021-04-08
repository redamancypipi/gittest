<template>
	<view>
		<uni-steps :options="[{title: '填写课程信息'}, {title: '添加章节'},{title: '发布课程'}]" :active="2">	
		</uni-steps>
		<button size="mini" style="margin-left: 300rpx;background-color: #F0AD4E;" @click="createSubject()">发布课程</button>
	</view>
</template>
<script>
	import uniSteps from '@/components/uni-steps/uni-steps.vue'
	export default {
	    components: {uniSteps},
		data() {
			return {
				
			}
		},
		methods: {
			createSubject(){
				//提交信息并跳转到下一步
				uni.switchTab({
					url:'../index/index'
				})
			}
		}
	}
	
</script>

<style>
	
</style>
