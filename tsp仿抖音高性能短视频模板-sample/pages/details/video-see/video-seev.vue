<template>
	<view class="content">
		<tw-videov ref="videoGroup" :loadOpen="false" :currentOpen="true" @lodData="loadingData" :tabBarShow="1" :totalvod="50"></tw-videov>
		<image src="/static/icon/black2.png" mode="" class="blackImg" :style="{top:(statusBarHeight + 10)+'px'}" @click="backBtn"></image>
		<view class="theory-layer" v-if="boardHeight > 0"></view>
		<view class="discuss f" :style="{bottom:boardHeight+'px',height:boardHeight >0 ?'60px':'50px'}" :class="[boardHeight > 0 ? 'discuss-active':'']">
			<view class="discuss-box f frc">
				<view class="discuss-left f frc" :class="[boardHeight > 0 ? 'discuss-left-active':'']">
					<view><input type="text" value="" placeholder="留下你的精彩评论吧" class="discuss-left-input"
					 :style="{color:boardHeight > 0 ?'#000':'#fff'}" :adjust-position="false"/></view>
					<view class="f frc">
						<text class="discuss-left-ej">@</text>
						<image src="/static/icon/biaoqing.png" mode="" class="discuss-left-img"></image>
					</view>
				</view>
				<view class="discuss-right">
					<image src="/static/icon/jiantou.png" mode="" class="discuss-right-img"></image>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	const deviceInfo = uni.getSystemInfoSync()
	import twVideov from '@/components/tsp-video/tsp-video-list/video-v.vue'
	import { getVodData } from '@/static/js/vodData.js'
	export default{
		components:{
			twVideov
		},
		data(){
			return {
				statusBarHeight:deviceInfo.statusBarHeight,
				seeVodIndex:0,
				seeVodList:[],
				pageNum:1,
				boardHeight:0,
				videoData: []
			}
		},
		onLoad(option) {
			this.videoData = getVodData()
			this.seeVodIndex = getApp().globalData.seeVodIndex
			this.seeVodList = getApp().globalData.seeVodList
			this.pageNum = option.pageNum
			console.log('this.seeVodIndex',this.seeVodIndex)
			uni.onKeyboardHeightChange(res => {
				this.boardHeight = res.height
			})
			setTimeout(()=>{
				this.initVod()
			},500)
		},
		onUnload() {
			uni.offKeyboardHeightChange((res)=>{})
		},
		onShow() {
			/* 播放视频 */
			if(this.$refs.videoGroup){
				this.$refs.videoGroup.showPlay()
			}
		},
		onHide() {
			/* 暂停视频 */
			if(this.$refs.videoGroup){
				this.$refs.videoGroup.hidePause()
			}
		},
		methods:{
			backBtn(){
				uni.navigateBack()
			},
			startData(){
				let num = this.seeVodList.length; //获取视频数据的长度
				/* 模拟请求 */
				return new Promise((resolve, reject)=>{
					setTimeout(()=>{
						let dataList = JSON.parse(JSON.stringify(this.videoData))
						if(this.pageNum <= 5 ){
							dataList.filter(item=>{
								/** 参数数据自行拼接  */
								item.vodUrl = item.src
								item.coverImg = item.coverImg //视频封面
								item.coverShow = true //是否显示视频封面，vue 小程序端不播放会显示视频，可以不用显示封面，App不播放不会显示视频，就需要封面了
								item.object_fit = item.object_fit //视频的显示类型
								item.sliderShow = true //是否显示进度条
								item.rotateImgShow = true //是否显示旋转头像
								item.fabulousShow = false//是否点赞
								item.followReally = false //是否已经关注
							})
							this.pageNum++
						}else{
							dataList = []
						}
						resolve(dataList)
					},1000)
				})
			},
			/* 初始加载视频 */
			initVod(){
				if(this.$refs.videoGroup && this.seeVodList.length > 0){
					/* 调用视频的初始方法 */
					this.$refs.videoGroup.initVod(this.seeVodList,this.seeVodIndex); //seeVodIndex是播放的下标
				}
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
		}
	}
</script>

<style>
	page{
		background-color: #000;
	}
	.content{
		width: 100%;
		min-height: 100%;
	}
	.blackImg{
		width: 45rpx;
		height: 45rpx;
		position: fixed;
		top: 0;
		left: 30rpx;
		z-index: 100;
	}
	.f{
		display: flex;
	}
	.frc{
		flex-direction: row;
		align-items: center;
	}
	.theory-layer{
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		z-index: 9;
	}
	.discuss{
		position: absolute;
		bottom: 0;
		width: 750rpx;
		left: 0;
		z-index: 10;
		height: 50px;
		background-color: #000;
		align-items: center;
		justify-content: center;
	}
	.discuss-active{
		background-color: #FFFFFF;
	}
	.discuss-box{
		width: 680rpx;
		height: 40px;
	}
	.discuss-left{
		background-color: #000;
		height: 40px;
		border-radius: 20px;
		flex: 1;
		padding: 0 30rpx;
		justify-content: space-between;
	}
	.discuss-left-active{
		background-color: #E5E5E3;
	}
	.discuss-left-input{
		height: 40px;
	}
	.discuss-left-ej{
		position: relative;
		top: -7rpx;
		font-size: 50rpx;
		font-weight: bold;
		color: #666666;
	}
	.discuss-left-img{
		width: 50rpx;
		height: 50rpx;
		margin-left: 15rpx;
	}
	.discuss-right{
		position: relative;
		width: 45rpx;
		height: 45rpx;
		margin-left: 10rpx;
		background-color: #FFFFFF;
		border-radius: 45rpx;
		justify-content: center;
	}
	.discuss-right-img{
		position: absolute;
		top: -10rpx;
		left: -10rpx;
		width: 65rpx;
		height: 65rpx;
		transform: rotate(-90deg);
	}
</style>
