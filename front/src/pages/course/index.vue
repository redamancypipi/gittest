<template>
    <view>
        
        <view>
            <uni-segmented-control :current="current" :values="items" 
            @clickItem="onClickItem" style-type="text" 
            active-color="#FFD700"></uni-segmented-control>
            <view class="content">
				<!-- 分段器 -->
                <view v-if="current === 0">
                         <chapter></chapter>   
                </view>
                <view v-if="current === 1">
                   <work></work> 
                </view>
                <view v-if="current === 2">
                    <file></file>
                </view>
                <view v-if="current === 3">
                   <student></student>
                </view>
                <view v-if="current === 4">
                   <comment></comment>
                </view>
            </view>
        </view>
    </view>
</template>
<script>
import chapter from './chapter';
import comment from './comment';
import student from './student';
import work from './work';
import file from './file';
import uniSegmentedControl from "@dcloudio/uni-ui/lib/uni-segmented-control/uni-segmented-control.vue";
export default {
    components:{
        chapter,
        comment,
        student,
        work,
        file,
        uniSegmentedControl
    },
    data() {
        return {
            items: ['章节','作业','资源','成员','评论'],
            current: 0
        }
    },
	onLoad: function(option){
		const courseId = uni.getStorageSync('courseId');
		// console.log(courseId)
		//15195078422
	},
    methods: {
        onClickItem(e) {
            if (this.current !== e.currentIndex) {
                this.current = e.currentIndex;
            }
        }
    }
}
</script>
<style lang="scss">
    
</style>