### 特别说明
- 1.推荐使用示例项目里的组件，直接下载导入插件可能官方会压缩代码，会出现点小问题
- 2.分页请求数据最少大于3条，不然会出现一些小问题

## 说明
- 免费使用
- 支持H5、小程序、APP(nvue)、目前只支持vue2版本，vue3版本后期更新
- H5、小程序端采用的是3个item节点循环加载显示，到第一个视频和最后一个视频自动禁止循环滚动加载，不用担心数据过多卡顿的问题
- 断网无法播放时显示进度条加载动效，有网络时自动加载播放
- 不会串音，可以指定某个视频预览播放
- 自定义进度条，可拖动播放
- 已经封装成组件，使用简单，开箱即用，不用过多更改

## 快速上手

1. 页面引入tsp-video库
```js
/* H5、小程序 */
import twVideov from '@/components/tsp-video/tsp-video-list/video-v.vue';
components:{
	twVideov
};
/* APP nvue */
import twVideon from '@/components/tsp-video/tsp-video-list/video-n.nvue';
components:{
	twVideon
};
```

2. 初始加载视频数据
```js
/* 调用视频的初始方法 */
this.$refs.videoGroup.initVod(data,0); //0是播放的下标（默认播放下标是0）
```

3. 下拉刷新
```js
/* 调用视频的重新加载方法 */
this.$refs.videoGroup.refreshSquare(data,0); //0是播放的下标（默认播放下标是0）
```

4. 到底加载
```js
/* 调用视频的到底加载方法方法 */
this.$refs.videoGroup.lodingData(data);
```

5. onShow()播放视频
```js
onShow() {
	/* 播放视频 */
	if(this.$refs.videoGroup){
		this.$refs.videoGroup.showPlay()
	}
}
```

6. onHide()暂停视频
```js
onHide() {
	/* 暂停视频 */
	if(this.$refs.videoGroup){
		this.$refs.videoGroup.hidePause()
	}
}
```

## 参数配置

| 属性                | 类型        | 默认值        | 说明                                                                   |
| ------------------- | -----------| ----------- | ---------------------------------------------------------------------- |
| loadOpen            | Boolean    | true        | 是否开启下拉刷新 |
| tabBarShow          | Number     | 0     		 | tabBar栏 0系统tabBar栏 1自定义tabBar栏                                                         |
| tabBarHeight        | Number     | 50          | 自定义底部栏的高度(在tabBarShow为1情况下生效)                                                           |
| speedBottom         | Number     | 0           | 进度条离底部的距离 px                                                       |
| autoplay            | Boolean    | true        | 初始加载完成是否自动播放                       |
| loopPlay            | Boolean    | true        | 当前视频是否循环播放                       |
| nextPlay            | Boolean    | false       | 是否开启自动播放下一条 （H5不支持，由于浏览器不支持视频自动播放，H5自动播放到下一条会黑屏）                       |
| totalvod            | Number     | 0           | 视频总数量，有值才能滑动加载到最后一个视频并禁止循环滑动（仅H5、小程序支持）                                        |
| doubleOpen          | Boolean    | true        | 是否开启双击点赞                                        |
| doubleHeart         | Boolean    | true        | 双击点赞屏幕是否显示桃心                                        |
| swId                | String     | ''          | 页面多个tab视频时需传入不同的类型                                |
| showBarHeight       | Boolean    | true        | 是否有显示 statusBarHeight 状态栏适配                                |

## 事件
| 属性                | 类型        | 默认值        | 说明                                                                   |
| ------------------- | -----------| ----------- | ---------------------------------------------------------------------- |
| @refreshData        | EventHandle| EventHandle | 下拉刷新加载回调                                                     |
| @lodData            | EventHandle| EventHandle | 到底加载回调                                          |
| @doubleClick        | EventHandle| EventHandle | 双击回调，event 当前双击的视频对象数据                                          |           
| @longpress          | EventHandle| EventHandle | 长按回调，event 当前长按的视频对象数据                                          |                                                


## 使用示例

```html
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
	import vodData from '@/static/js/vodData.js' //假数据
	export default{
		components:{
			twVideov
		},
		data(){
			return {
				videoData: vodData,
				autoplay:true,
				nextPlay:false,
				loopPlay:true
			}
		},
		onLoad() {
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

```

## 联系我
- 视频播放核心源代码已加密，需要核心源代码的可联系我，开发不易，源代码需要额外赞助一下才单独开放
- qq：1670280472