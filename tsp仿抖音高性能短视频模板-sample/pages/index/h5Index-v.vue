<template>
	<view class="pageBox">
		<tw-videov ref="videoGroup" @lodData="loadingData" @refreshData="refreshData" :autoplay="autoplay" :nextPlay="nextPlay" :loopPlay="loopPlay"
		 @doubleClick="doubleClick" @longpress="longpress"></tw-videov>
		<view class="automatic" @click="openAutomatic">{{nextPlay?'关闭自动播放':'开启自动播放'}}</view>
	</view>
</template>

<script>
	/* 
	 * vue页面引用
	 */
	import twVideov from '@/components/tsp-video/tsp-video-list/video-v.vue'
	import { getVodData } from '@/static/js/vodData.js' //假数据
	export default{
		components:{
			twVideov
		},
		data(){
			return {
				videoData: [],
				autoplay:true,
				nextPlay:false,
				loopPlay:true
			}
		},
		onLoad() {
			this.videoData = getVodData()
			// #ifdef H5
			this.autoplay = false
			// #endif
			this.initVod()
		},
		onShow() {
			/* 播放视频 */
			if(this.$refs.videoGroup){
				this.$refs.videoGroup.showPlay()
				this.$refs.videoGroup.muteVideo(false) //取消视频播放设置为静音，解决切换到其他页面后因为网络问题还在有声音播放
			}
		},
		onHide() {
			/* 暂停视频 */
			if(this.$refs.videoGroup){
				this.$refs.videoGroup.hidePause()
				this.$refs.videoGroup.muteVideo(true) //视频播放设置为静音，解决切换到其他页面后因为网络问题还在有声音播放
			}
		},
		methods:{
			startData(){
				let list = []
				/* 模拟请求 */
				return new Promise((resolve, reject)=>{
					setTimeout(()=>{
						let dataList = JSON.parse(JSON.stringify(this.videoData))
						dataList.filter(item=>{
							/** 参数数据自行拼接  */
							item.vodUrl = item.src
							item.coverImg = item.coverImg //视频封面
							item.coverShow = false //是否显示视频封面，vue 小程序端不播放会显示视频，可以不用显示封面，App不播放不会显示视频，就需要封面了
							item.object_fit = item.object_fit //视频的显示类型
							item.sliderShow = true //是否显示进度条
							item.rotateImgShow = true //是否显示旋转头像
							item.fabulousShow = false//是否点赞
							item.followReally = false //是否已经关注
						})
						resolve(dataList)
					},500)
				})
			},
			/* 初始加载视频数据 */
			initVod(){
				this.startData().then((res)=>{
					if(res.length > 0){
						/* 调用视频的初始方法 */
						this.$refs.videoGroup.initVod(res,0); //0是播放的下标（默认播放下标是0）不需要指定视频播放可不传
					}
				})
			},
			/* 下拉刷新 */
			refreshData(){
				this.startData().then((res)=>{
					if(res.length > 0){
						/* 调用视频的重新加载方法 */
						setTimeout(()=>{
							this.$refs.videoGroup.refreshSquare(res,0); //0是播放的下标（默认播放下标是0）不需要指定视频播放可不传
						},2000)
					}
				})
			},
			/* 上拉加载 */
			loadingData(){
				this.startData().then((res)=>{
					if(res.length > 0){
						/* 调用视频的到底加载方法方法 */
						this.$refs.videoGroup.lodingData(res);
					}
				})
			},
			/* 双击回调 */
			doubleClick(event){
				// console.log('双击',event)
			},
			/* 长按当前视频回调 */
			longpress(event){
				// console.log('长按',event)
			},
			/* 是否开启自动播放 */
			openAutomatic(){
				this.nextPlay = !this.nextPlay
				this.loopPlay = this.nextPlay ? false : true
			}
		}
	}
</script>

<style>
	.pageBox{
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-color: #000;
	}
	.automatic{
		position: absolute;
		z-index: 20;
		top: 120rpx;
		left: 50rpx;
		font-size: 32rpx;
		color: blue;
	}
	.automatic:active{
		transform: scale(0.8);
		transition: all 0.3s linear;
	}
</style>
