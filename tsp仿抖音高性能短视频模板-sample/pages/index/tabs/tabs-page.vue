<template>
	<view class="tabsPage" v-if="firstLoaded">
		<!-- 视频块 -->
		<view class="tabsPage-item" v-if="pageIndex != 0">
			<appIndex ref="vodRef" :swId="tabItem.id" :tabIndex="tabIndex" :pageIndex="pageIndex" :tabItem="tabItem"></appIndex>
		</view>
		<!-- 商城块 -->
		<view v-if="pageIndex == 0">
			<view :style="{height:statusBarHeight+'px'}"></view>
			<text style="color: #fff;" v-for="(item,index) in 10" :key="index">商城</text>
		</view>
	</view>
</template>

<script>
	import appIndex from '../appIndex-n.nvue'
	export default{
		components:{
			appIndex
		},
		props: {
			/* 当前tabsPage的下标 */
			pageIndex:{
				type: Number,
				default:0
			},
			/* 当前tab栏下标 */
			tabIndex:{
				type: Number,
				default:0
			},
			/* 当前tab栏数据 */
			tabItem:{
				type: Object,
				default:()=>{}
			},
			statusBarHeight:{
				type: Number,
				default:0
			}
		},
		data(){
			return {
				// 当前组件是否已经加载过了
				firstLoaded: false,
			}
		},
		watch:{
			tabIndex(val){
				if(val == this.pageIndex){
					if(this.$refs.vodRef){
						this.showPlay()
					}
				}else{
					if(this.$refs.vodRef){
						this.hidePause()
					}
				}
			}
		},
		methods:{
			/* 初始加载 */
			loadTabData(){
				this.firstLoaded = true
			},
			/* onShow 播放视频 */
			showPlay(){
				this.$refs.vodRef.assemblyOnShow()
			},
			/* onHide 暂停视频 */
			hidePause(){
				this.$refs.vodRef.assemblyOnHide()
			},
			/* tabVideo进度条滑动事件 */
			tabPageTouchmoveSlider(event){
				this.$refs.vodRef.appVodTouchmoveSlider(event);
			},
			/* tabVideo进度条滑动结束事件 */
			tabPageTouchendSlider(event){
				this.$refs.vodRef.appVodTouchendSlider(event);
			},
		}
	}
</script>

<style scoped>
	.tabsPage{
		/* #ifndef APP-NVUE */
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		display: flex;
		/* #endif */
		flex: 1;
	}
	.tabsPage-item{
		flex: 1;
	}
</style>