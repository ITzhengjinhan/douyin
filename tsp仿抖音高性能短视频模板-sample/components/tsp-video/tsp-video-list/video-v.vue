<template>
	<view class="page">
		<view :style="{height:videoStyle.statusBarHeight}"></view>
		<view class="contentBox" v-if="contentShow" :style="{width:videoStyle.width,height:videoStyle.height}">
			<swiper :style="{width:videoStyle.width,height:videoStyle.height}" vertical :circular="circular" :duration="durationNum" @change="changeSwiper" @animationfinish="swiperVod" class="swiperBox" :current="currentIndex">
				<swiper-item v-for="(item,index) in vodList" :key="index" class="box">
					<view :style="{opacity:shadeNum > 2 ?'0':'1',width:videoStyle.width,height:videoStyle.height}">
						<view class="vodPlayer">
							<video :style="{width:videoStyle.width,height:videoStyle.height}" :src="item.vodUrl"
							:controls="false"
							:enable-progress-gesture="false"
							:show-loading="false" 
							:show-play-btn="false" 
							:show-center-play-btn="false"
							:show-fullscreen-btn="false"
							:vslide-gesture-in-fullscreen="false"
							:show-progress="false"
							:object-fit="item.object_fit"
							:http-cache="true"
							:loop="loopPlay"
							:muted="(index == vodIndex && !muteSetup) ? false : true"
							 :id="'myVideo'+index+swId" @play="startPlay(index)" @waiting="bufferVod(index)" @timeupdate="timeupdateVod($event,index)" @error="errVod(index)" @ended="endedVod(index)">
							</video>
						</view>
						<!-- 视频封面 -->
						<view class="vodPause-img" v-if="item.coverShow">
							<image :src="item.coverImg" :mode="item.object_fit == 'cover'?'aspectFill':'widthFix'" class="cover-img"
							 :style="{opacity:item.coverOpacity?1:0,width:videoStyle.width,height:item.object_fit == 'cover'?videoStyle.height:0}"></image>
						</view>
						<!-- 暂停图标 -->
						<view class="vodPause-img" v-if="item.pauseShow">
							<image src="/static/icon/vod-play.png" class="vodPauseImage"></image>
						</view>
						<view @click="handClick($event,index)" @longpress="longpress(item)" class="vodPlayView" :style="videoStyle" @touchstart="vodViewStart($event)"
						 @touchmove="vodViewMove($event)" @touchend="vodViewEnd($event)"></view>
						<!-- 底部标题、右侧操作栏 -->
						<videoMenu :ref="'menuRef'+index" :vodIndex="vodIndex" :vodCurIndex="vodCurIndex" :item="item" :discussNum="totalPlayList.length" :index="index"
						 :sliderDrag="sliderDrag" :moveOpacity="moveOpacity" :palyCartoon="palyCartoon" @handleInfo="handleInfo"></videoMenu>
					</view>
					<!-- 底层图标 -->
					<view class="vodLayer">
						<image src="/static/icon/sound.png" mode="" class="vodLayer-img"></image>
					</view>
				</swiper-item>
			</swiper>
			<!-- 遮罩层，防止滑动过快 -->
			<swiper class="shade-swiper" :style="{width:videoStyle.width,height:videoStyle.height,pointerEvents:showShade?'auto':'none',opacity:showShade?'1':'0'}" vertical>
				<swiper-item v-for="(item,index) in 1" :key="index" class="box">
					<!-- 底层图标 -->
					<view class="vodLayer">
						<image src="/static/icon/sound.png" mode="" class="vodLayer-img"></image>
					</view>
				</swiper-item>
			</swiper>
			<!-- 底部加载动画 -->
			<view class="pullup" v-if="loadShow">
				<view style="width: 32rpx;height: 32rpx;margin-right: 30rpx;">
					<view class="k-ball7a" style="width: 20rpx;height: 20rpx;"></view>
					<view class="k-ball7b" style="width: 20rpx;height: 20rpx;"></view>
					<view class="k-ball7c" style="width: 20rpx;height: 20rpx;"></view>
					<view class="k-ball7d" style="width: 20rpx;height: 20rpx;"></view>
				</view>
			</view>
			<view class="flooter" :style="{opacity:moveOpacity ? 0 : 1,bottom:speedBottom+'px'}" v-if="vodList.length > 0 && beforeVodInfo.sliderShow">
				<!-- 滑动进度条显示时间 -->
				<view class="sliderData" v-if="sliderDrag">
					<view class="slider-text">
						<text>{{formatSeconds(sliderTime)}}</text>
						<text style="font-size: 30rpx;margin: 0 10rpx;">/</text>
						<text style="opacity: 0.5;">{{formatSeconds(videoTime)}}</text>
					</view>
				</view>
				<!-- 进度条 -->
				<view class="sliderBox" :style="{opacity:shadeNum > 2 ?'0':'1'}" @touchmove.stop="touchmoveSlider" @touchstart.stop="touchmoveSlider"
				 @touchend.stop="touchendSlider" v-if="vodList.length > 0 && beforeVodInfo.sliderShow">
					<view class="slider-item" :style="{height: brightSlider?'18px':'2px'}">
						<!-- 底层 -->
						<view class="slider-box" :class="[brightSlider?'slider-box-active':'']"></view>
						<!-- 滑块 -->
						<!-- <view class="slider-block" :class="[brightSlider?'slider-block-active':'']" :style="{transform:`translateX(${sliderProgress}px)`}"></view> -->
						<!-- 滑动层 -->
						<view class="slider-load" :class="[brightSlider?'slider-load-active':'']" :style="{transform: `translateX(${sliderProgress > 0 ? sliderProgress - screenWidth : -screenWidth}px)`}"></view>
					</view>
				</view>
				<view v-else :style="{width:screenWidth+'px',height:'18px'}"></view>
				<!-- loading加载动画进度 -->
				<view class="loadSlider" v-if="vodList.length > 0 && beforeVodInfo.loadingShow">
					<view :style="{opacity:brightSlider ? 0 : 1}">
						<view class="loading-slider" ref="loadSlider" :style="{width:screenWidth+'px'}"></view>
					</view>
				</view>
			</view>
		</view>
		<!-- 刷新 -->
		<view class="refresh" v-if="refreshShow">
			<view style="height: 140rpx;"></view>
			<!--  :style="{opacity:refreshOpacity,transform: 'translateY('+refreshclientY+'px)'}" -->
			<view class="refreshBox" :style="{opacity:refreshOpacity}">
				<view style="width: 32rpx;height: 32rpx;"></view>
				<view>下拉刷新内容</view>
				<view style="width: 32rpx;height: 32rpx;margin-right: 30rpx;">
					<view class="k-ball7a k-paused" style="width: 20rpx;height: 20rpx;" :class="[refreshOpen?'k-running':'']"></view>
					<view class="k-ball7b k-paused" style="width: 20rpx;height: 20rpx;" :class="[refreshOpen?'k-running':'']"></view>
					<view class="k-ball7c k-paused" style="width: 20rpx;height: 20rpx;" :class="[refreshOpen?'k-running':'']"></view>
					<view class="k-ball7d k-paused" style="width: 20rpx;height: 20rpx;" :class="[refreshOpen?'k-running':'']"></view>
				</view>
			</view>
		</view>
		<!-- 点赞桃心 -->
		<view class="doubleImage" v-for="(item,index) in likeList" :key="index" :ref="item.id+'Ref'" :class="[item.className]"
		 :style="{width: item.width,height:item.height,top:item.top,left:item.left}">
			<image src="/static/icon/selectTaoxin.png" v-if="item.isShow" :style="{width: item.width,height:item.height,transform: `rotate(${item.rotate})`}" />
		</view>
	</view>
</template>
<script>
	import videoJs from './video-v-encipher.js'
	import videoMenu from '../tsp-menu/tsp-menu-v.vue'
	export default {
		components: {
			videoMenu
		},
		mixins:[videoJs]
	}
</script>

<style>
	.page{
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		overflow: hidden;
	}
	.contentBox{
		position: relative;
	}
	.shade-swiper{
		position: absolute;
		pointer-events: none;
		opacity: 0;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		z-index: 99;
	}
	.swiperBox{
		position: relative;
		z-index: 2;
	}
	.box{
		position: relative;
	}
	.vodLayer{
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		z-index: 1;
		background-color: #000000;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.vodLayer-img{
		width: 150rpx;
		height: 150rpx;
		opacity: 0.8;
	}
	.cover-img{
		width: 750rpx;
		height: auto;
	}
	.vodPlayer{
		position: absolute;
		top: 0;
		left: 0;
		z-index: 2;
		width: 100%;
		height: 100%;
	}
	.vodPlayView{
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-color: rgba(0,0,0,0.1);
		z-index: 7;
	}
	.vodPause-img{
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-color: rgba(0,0,0,0.1);
		z-index: 4;
		display: flex;
		align-items: center;
		justify-content: center;
		overflow: hidden;
	}
	.vodPauseImage{
		width: 134rpx;
		height: 134rpx;
		opacity: 0.35;
		animation: vodPauseName 0.1s linear forwards;
	}
	
	@keyframes vodPauseName{
		0%{
			transform: scale(2);
			opacity: 0;
		}
		100%{
			transform: scale(1);
			opacity: 0.35;
		}
	}
	/* loading动画 */
	.loadingView{
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		z-index: 100;
		display: flex;
		align-items: center;
		justify-content: center;
		background-color: #000000;
	}
	.k-ball7a {
		border:0;
		margin:0;
		width:25rpx;
		height:25rpx;
		position:absolute;
		border-radius:50%;
		animation:k-loadingO 2s ease infinite;
		background:#4CD964;
		animation-delay:-1.5s
	}
	.k-ball7b {
		border:0;
		margin:0;
		width:25rpx;
		height:25rpx;
		position:absolute;
		border-radius:50%;
		animation:k-loadingO 2s ease infinite;
		background:#F63D3A;
		animation-delay:-1s
	}
	.k-ball7c {
		border:0;
		margin:0;
		width:25rpx;
		height:25rpx;
		position:absolute;
		border-radius:50%;
		animation:k-loadingO 2s ease infinite;
		background:#FDA543;
		animation-delay:-0.5s
	}
	.k-ball7d {
		border:0;
		margin:0;
		width:25rpx;
		height:25rpx;
		position:absolute;
		border-radius:50%;
		animation:k-loadingO 2s ease infinite;
		background:#5C6BC0
	}
	.k-paused{
		animation-play-state: paused;
	}
	.k-running{
		animation-play-state: running;
	}
	@keyframes k-loadingO {
		0%,100% {
			transform:translate(0)
		}
		25% {
			transform:translate(160%)
		}
		50% {
			transform:translate(160%,160%)
		}
		75% {
			transform:translate(0,160%)
		}
	}
	/* 进度条 */
	.flooter{
		position: absolute;
		bottom: 0;
		left: 0;
		z-index: 1100;
		transition: all 0.3s linear; 
	}
	.sliderBox{
		position: relative;
		min-height: 18px;
		width: 750rpx;
	}
	.slider-item{
		width: 750rpx;
		min-height: 2px;
		position: absolute;
		left: 0;
		bottom: 0;
		overflow: hidden;
	}
	.slider-box{
		height: 2px;
		background-color: #C8C7CC;
		opacity: 0.2;
		border-radius: 2px;
	}
	.slider-block{
		position: absolute;
		width: 5px;
		height: 5px;
		border-radius: 5px;
		background-color: #FFFFFF;
		top: -1px;
		left: 0;
		z-index: 3;
		opacity: 0.5;
	}
	.slider-load{
		position: absolute;
		width: 100%;
		bottom: 0;
		height: 2px;
		background-color: #FFFFFF;
		opacity: 0.4;
		z-index: 3;
		border-radius: 2px;
		transition: all 0.02s linear; 
	}
	.slider-box-active{
		position: absolute;
		width: 100%;
		bottom: 0;
		height: 7px;
		background-color: #C8C7CC;
		opacity: 0.4;
		border-radius: 7px;
		transition: height 0.2s linear;
	}
	.slider-block-active{
		position: absolute;
		top: 8px;
		width: 15px;
		height: 15px;
		border-radius: 15px;
		opacity: 1;
		left: -3px;
		background-color: #FFFFFF;
	}
	.slider-load-active{
		width: 100%;
		opacity: 1;
		height: 7px;
		border-radius: 7px;
		transition: height 0.2s linear;
	}
	.sliderData{
		width: 750rpx;
		height: 200rpx;
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: center;
		color: #FFFFFF;
		font-size: 50rpx;
		font-weight: bold;
	}
	.slider-text{
		flex-direction: row;
		display: flex;
		align-items: center;
	}
	/* 下拉刷新 */
	.refresh{
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		z-index: 15;
	}
	.refreshBox{
		height: 100rpx;
		color: #FFFFFF;
		font-size: 33rpx;
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: space-between;
		padding: 0 30rpx;
		/* transform: translateY(-30px); */
		transition: all 0.01s linear;
		opacity: 0;
	}
	.pullup{
		position: absolute;
		left: 0;
		bottom: 0;
		z-index: 1;
		height: 200rpx;
		opacity: 1;
		width: 100%;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.loadSlider{
		position: absolute;
		bottom: 0px;
		width: 750rpx;
		height: 2px;
		align-items: center;
	}
	.loading-slider{
		width: 750rpx;
		height: 2px;
		background-color: #FFFFFF;
		opacity: 0.6;
		animation: sliderName 0.4s linear infinite;
	}
	@keyframes sliderName{
		0%{
			opacity: 0.9;
			transform: scale(0.4);
		}
		100%{
			opacity: 0.1;
			transform: scale(1);
		}
	}
	.doubleImage{
		pointer-events: none;
		position: absolute;
		top: 0;
		left: 0;
		z-index: 20;
		/* width: 170rpx;
		height: 170rpx; */
		animation: doubleName 0.3s linear forwards;
		transform: scale(0);
		opacity: 0;
	}
	@keyframes doubleName{
		0%{
			transform: scale(1.6);
			opacity: 0.8;
		}
		50%{
			transform: scale(1);
			opacity: 1;
		}
		75%{
			transform: scale(1.02);
			opacity: 1;
		}
		100%{
			transform: scale(1);
			opacity: 1;
		}
	}
	.doubleImageRemove{
		animation: doubleRemove 0.4s linear forwards;
	}
	@keyframes doubleRemove{
		0%{
			transform: scale(1) translateY(0rpx);
			opacity: 1;
		}
		50%{
			transform: scale(1.35) translateY(-50rpx);
			opacity: 1;
		}
		100%{
			transform: scale(1.8) translateY(-100rpx);
			opacity: 0;
		}
	}
</style>
