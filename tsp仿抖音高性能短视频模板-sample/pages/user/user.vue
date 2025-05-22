<template>
	<view class="page">
		<view class="user-black" v-if="balckShow" :style="{opacity:balckOpacity?'1':'0'}">
			<view :style="{height:statusBarHeight+'px'}"></view>
			<view class="f user-black-box">
				<view class="user-black-left"></view>
				<view class="user-black-cont">卧槽无情的电影</view>
				<view class="user-black-follow">
					<!-- #ifdef APP-PLUS -->
					<view class="f user-black-follow-btn">关注</view>
					<!-- #endif -->
				</view>
			</view>
		</view>
		<view class="header">
			<image src="/static/icon/touxiang.jpg" mode="widthFix" class="header-bg"></image>
			<view class="header-layer f"></view>
		</view>
		<view class="userBox">
			<view class="user-top f">
				<view class="user-avatar f">
					<image src="/static/icon/touxiang.jpg" mode="" class="avatar-image"></image>
				</view>
				<view class="user-item">
					<view><text class="user-title">获赞</text></view>
					<view class="mg-t1"><text class="user-num">1059.1w</text></view>
				</view>
				<view class="user-silder"></view>
				<view class="user-item">
					<text class="user-title">关注</text>
					<view class="mg-t1"><text class="user-num">59.1w</text></view>
				</view>
				<view class="user-silder"></view>
				<view class="user-item">
					<text class="user-title">粉丝</text>
					<view class="mg-t1"><text class="user-num">59.1w</text></view>
				</view>
			</view>
			<view class="user-news">
				<view><text class="user-name">卧槽无情</text></view>
				<view class="mg-t1"><text class="user-code">号：123456123456</text></view>
			</view>
			<view class="user-occupy"></view>
			<view class="user-cont">
				<view>
					<text class="user-cont-size">卧槽无情卧槽无情卧槽无情卧槽无情卧槽无情卧槽无情卧槽无情卧槽无情</text>
				</view>
			</view>
			<view class="user-follow f">
				<text class="user-follow-text">关注</text>
			</view>
		</view>
		<view class="user-navtab" :style="{top:(statusBarHeight + 40)+'px'}">
			<view class="user-nav f">
				<view class="user-nav-item f" :class="[navType == 0?'active-nav-view':'']" @click="changeNav(0)">作品</view>
				<view class="user-nav-item f" :class="[navType == 1?'active-nav-view':'']" @click="changeNav(1)">喜欢</view>
			</view>
		</view>
		<view class="user-flotter">
			<!-- 作品 -->
			<view v-if="navType == 0">
				<view class="user-works f">
					<view class="user-works-item" v-for="item in 30" :key="item.index"></view>
				</view>
			</view>
			<!-- 喜欢 -->
			<view v-if="navType == 1">
				<!-- <view class="user-works f">
					<view class="user-works-item"></view>
					<view class="user-works-item"></view>
					<view class="user-works-item"></view>
					<view class="user-works-item"></view>
				</view> -->
				<view class="f user-like">
					<view class="user-like-image f"><image src="/static/icon/lock.png" mode="" class="likeImg"></image></view>
					<view class="user-like-title">喜欢的内容不可见</view>
					<view class="user-like-text">该用户已将喜欢列表设为私密</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	const deviceInfo = uni.getSystemInfoSync()
	// console.log(deviceInfo)
	export default{
		data(){
			return {
				statusBarHeight:deviceInfo.statusBarHeight,
				balckShow:false, //是否显示标题导航
				balckOpacity:false,
				navType:0,
			}
		},
		onLoad() {
			
		},
		onPageScroll(e) {
			if(e.scrollTop >= 5){
				this.balckShow = true
			}else{
				this.balckShow = false
			}
			if(e.scrollTop >= 100){
				this.balckOpacity = true
			}else{
				this.balckOpacity = false
			}
		},
		onShow() {
			
		},
		methods:{
			backBtn(){
				uni.navigateBack()
			},
			changeNav(e){
				this.navType = e;
			}
		}
	}
</script>

<style>
	.f{
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
	}
	.page{
		background-color: #FFFFFF;
		flex: 1;
	}
	.mg-t1{
		margin-top: 10rpx;
	}
	.blackImg{
		width: 70rpx;
		height: 70rpx;
		position: absolute;
		top: 0;
		left: 30rpx;
	}
	.header{
		background-color: #eee;
		height: 350rpx;
		position: relative;
		overflow: hidden;
	}
	.header-bg{
		width: 750rpx;
		height: auto;
	}
	.header-layer{
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-color: rgba(0,0,0,0.2);
	}
	.userBox{
		position: relative;
		background-color: #FFFFFF;
		border-top-right-radius: 30rpx;
		border-top-left-radius: 30rpx;
		margin-top: -50rpx;
	}
	.user-top{
		flex-direction: row;
		align-items: center;
		padding-left: 30rpx;
	}
	.user-avatar{
		width: 150rpx;
		height: 150rpx;
		border-radius: 150rpx;
		align-items: center;
		justify-content: center;
		margin-top: -40rpx;
		background-color: #FFFFFF;
	}
	.avatar-image{
		width: 150rpx;
		height: 150rpx;
		border-radius: 150rpx;
	}
	.user-item{
		width: 175rpx;
		margin-top: 20rpx;
		margin-left: 10rpx;
	}
	.user-title{
		/* #ifndef APP-NVUE */
		display: inline-block;
		width: 175rpx;
		/* #endif */
		text-align: center;
		font-size: 27rpx;
		color: #666;
	}
	.user-silder{
		width: 2rpx;
		height: 75rpx;
		background-color: #E5E5E3;
		position: relative;
		top: 10rpx;
	}
	.user-num{
		/* #ifndef APP-NVUE */
		display: inline-block;
		width: 175rpx;
		/* #endif */
		text-align: center;
		font-size: 35rpx;
		font-weight: bold;
	}
	.user-news{
		margin: 50rpx 0 0 30rpx;
	}
	.user-name{
		font-size: 40rpx;
		font-weight: bold;
	}
	.user-code{
		font-size: 28rpx;
	}
	.user-occupy{
		margin: 20rpx 30rpx;
		height: 1rpx;
		background-color: #E5E5E3;
	}
	.user-cont{
		margin: 0 30rpx;
	}
	.user-cont-size{
		font-size: 30rpx;
	}
	.user-follow{
		margin:30rpx;
		height: 80rpx;
		background-color: #FF2255;
		align-items: center;
		justify-content: center;
		border-radius: 5rpx;
	}
	.user-follow-text{
		font-size: 33rpx;
		color: #FFFFFF;
	}
	.user-navtab{
		height: 105rpx;
		position: -webkit-sticky;
		position: sticky;
		z-index: 100;
		background-color: #FFFFFF;
	}
	.user-nav{
		height: 100rpx;
		border-bottom-width: 1px;
		border-bottom-style: solid;
		border-color: #E5E5E3;
		flex-direction: row;
	}
	.user-nav-item{
		flex: 1;
		align-items: center;
		justify-content: center;
		text-align: center;
		font-size: 33rpx;
		color: #666;
		border-bottom-width: 2px;
		border-bottom-style: solid;
		border-color: #FFFFFF;
	}
	.active-nav-view{
		border-bottom-width: 2px;
		border-bottom-style: solid;
		border-color: #000;
		font-weight: bold;
		color: #000000;
	}
	.user-works{
		flex-direction: row;
		align-items: center;
		flex-wrap: wrap;
	}
	.user-works-item{
		width: 246rpx;
		height: 300rpx;
		background-color: #EEEEEE;
		margin: 5rpx 5rpx 0 0;
	}
	.user-flotter{
		min-height: 500rpx;
	}
	.user-like{
		min-height: 500rpx;
		align-items: center;
		justify-content: center;
		flex-direction: column;
	}
	.user-like-image{
		width: 100rpx;
		height: 100rpx;
		background-color: #E5E5E3;
		border-radius: 100rpx;
		align-items: center;
		justify-content: center;
	}
	.likeImg{
		width: 50rpx;
		height: 50rpx;
	}
	.user-like-title{
		margin-top: 30rpx;
		font-size: 35rpx;
		font-weight: bold;
	}
	.user-like-text{
		margin-top: 20rpx;
		font-size: 28rpx;
		color: #666;
	}
	/* 顶部标题栏 */
	.user-black{
		position: fixed;
		background-color: #FFFFFF;
		z-index: 100;
		top: 0;
		left: 0;
		opacity: 0;
		/* transition: all 0.2s linear; */
	}
	.user-black-box{
		height: 100rpx;
		width: 750rpx;
		flex-direction: row;
		align-items: center;
		justify-content: space-between;
	}
	.user-black-left{
		position: relative;
		top: 7rpx;
		width: 50rpx;
		height: 50rpx;
		margin-left: 30rpx;
	}
	.blackLeftImg{
		width: 25rpx;
		height: 40rpx;
	}
	.user-black-cont{
		width: 450rpx;
		text-align: center;
	}
	.user-black-follow{
		width: 100rpx;
		height: 50rpx;
		margin-right: 30rpx;
	}
	.user-black-follow-btn{
		width: 100rpx;
		height: 50rpx;
		background-color: #FF0000;
		color: #FFFFFF;
		font-size: 30rpx;
		border-radius: 5rpx;
		align-items: center;
		justify-content: center;
	}
</style>
