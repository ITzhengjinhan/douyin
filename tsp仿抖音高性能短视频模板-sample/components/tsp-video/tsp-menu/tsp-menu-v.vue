<template>
	<view class="menuPage">
		<!-- 底部标题 -->
		<view class="footTitle" :class="[vodIndex == index?(sliderDrag?'vodMenu-bright1':(moveOpacity?'vodMenu-bright2':'vodMenu-bright0')):'']">
			<view><text class="foot-name">@卧槽无情</text></view>
			<view><text class="foot-cont">开发不易，如果对你有帮助，请给个五星好评！</text></view>
			<view style="width: 300rpx;">
				<text class="foot-primary">卧槽无情的原声-卧槽</text>
			</view>
		</view>
		<!-- 右侧操作栏 -->
		<view class="menuBox">
			<view class="vodMenu" :class="[vodIndex == index?(sliderDrag?'vodMenu-bright1':(moveOpacity?'vodMenu-bright2':'vodMenu-bright0')):'']">
				<!-- 头像 -->
				<view class="menu-avatar">
					<image src="/static/icon/touxiang.jpg" mode="" class="avatar-image" @click="JumpBtn(1)"></image>
					<view class="follow" @click="followBtn(index)" v-if="!item.followReally" :class="{followHide:followShow==2}">
						<image src="/static/icon/gou.png" mode="" class="follow-guanzhu guanzhu-gou" v-if="followShow == 1 || followShow == 2"></image>
						<image src="/static/icon/guanzhu.png" mode="" class="follow-guanzhu" v-if="followShow==null || followShow==0" :class="{followActive:followShow==0}"></image>
					</view>
				</view>
				<!-- 点赞 -->
				<view class="fabulous" @click="JumpBtn(2)">
					<view class="fabulous-image" :class="likeeMeans" @click="fabulousBtn(index)" style="position: relative;">
						<image src="/static/icon/selectTaoxin.png" mode="" class="fabulous-image" v-if="item.fabulousShow"></image>
						<image src="/static/icon/taoxin.png" mode="" class="fabulous-image" v-else></image>
						<view class="like-pellet" :class="[likeeffect?'like-pellet-active':'']"></view>
					</view>
					<view class="fabulous-num">{{vodCurIndex}}</view>
				</view>
				<!-- 评论 -->
				<view class="fabulous" style="margin-top: 30rpx;" @click="JumpBtn(3)">
					<view class="fabulous-image">
						<image src="/static/icon/pinlun.png" mode="" class="fabulous-image"></image>
					</view>
					<view class="fabulous-num">{{discussNum}}</view>
				</view>
				<!-- 转发 -->
				<view class="fabulous" style="margin-top: 30rpx;" @click="JumpBtn(4)">
					<view class="fabulous-image">
						<image src="/static/icon/ward.png" mode="" class="fabulous-image"></image>
					</view>
					<view class="fabulous-num">转发</view>
				</view>
			</view>
		</view>
		<!-- 旋转头像 -->
		<view class="avatarMenu" v-if="item.rotateImgShow" :class="[vodIndex == index?(sliderDrag?'vodMenu-bright1':(moveOpacity?'vodMenu-bright2':'vodMenu-bright0')):'']">
			<view class="rotate-avatar k-paused" :class="[item.vodPaly && palyCartoon?'k-running':'']" @click="JumpBtn(5)">
				<image src="/static/icon/touxiang.jpg" mode="" class="rotate-image"></image>
			</view>
		</view>
	</view>
</template>

<script>
	export default{
		props:{
			//下标索引
			index:{
				type:Number,
				default:0
			},
			//当前swiper的下标
			vodIndex:{
				type:Number,
				default:0
			},
			//当前播放的视频下标
			vodCurIndex:{
				type:Number,
				default:0
			},
			//当前视频的整个对象
			item:{
				type:Object,
				default:()=>{return {}}
			},
			//数据总数
			discussNum:{
				type:Number,
				default:0
			},
			//是否在拖动进度
			sliderDrag:{ 
				type:Boolean,
				default:false
			},
			//是否透明
			moveOpacity:{
				type:Boolean,
				default:false
			},
			//是否播放旋转头像
			palyCartoon:{
				type:Boolean,
				default:false
			}
		},
		data(){
			return {
				fabulousShow:false,
				followShow:null,
				followOpen:false,
				fabuTimeOut:null,
				likeeffect:null, //点赞动效判断
			}
		},
		computed:{
			likeeMeans(){
				return this.likeeffect == true ? 'fabulous-taoxin' : this.likeeffect == false ?'cancel-taoxin' : ''
			}
		},
		methods:{
			/* 视频点赞动效 */
			fabulousBtn(index){
				if(this.item.coverOpacity) return
				let obj = Object.assign({},this.item)
				obj.fabulousShow = !obj.fabulousShow
				this.likeeffect = obj.fabulousShow
				this.$emit('handleInfo',obj)  //点赞成功
				
				/* clearTimeout(this.fabuTimeOut)
				this.fabuTimeOut = setTimeout(()=>{
					console.log('发送请求')
				},300) */
			},
			/* 关注动效 */
			followBtn(index){
				if(this.followOpen) return
				this.followOpen = true
				let obj = Object.assign({},this.item)
				obj.followReally = true
				this.followShow = 0
				setTimeout(()=>{
					this.followShow = 1
					setTimeout(()=>{
						this.followShow = 2
						setTimeout(()=>{
							this.followOpen = false
							this.followShow = null
							this.$emit('handleInfo',obj)  //关注成功
						},300)
					},100)
				},500)
			},
			/* 点击右侧菜单选项 1头像 2点赞 3评论 4转发 5旋转头像 */
			JumpBtn(index){
				switch(index){
					case 1 :
						console.log('点击头像')
						uni.navigateTo({
							url:'/pages/details/details-v'
						})
					break;
					case 2 :
						console.log('点击点赞')
					break;
					case 3 :
						console.log('点击3评论')
					break;
					case 4 :
						console.log('点击4转发')
					break;
					case 5 :
						console.log('5旋转头像')
					break;
				}
			}
		}
	}
</script>

<style>
	.menuPage{
		position: absolute;
		width: 100%;
		height: 100%;
		top: 0;
		left: 0;
	}
	/* 右边菜单栏 */
	.menuBox{
		position: absolute;
		right: 10rpx;
		width: 115rpx;
		height: 100%;
		z-index: 8;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.avatarMenu{
		position: absolute;
		bottom: 20px;
		right: 10rpx;
		width: 115rpx;
		z-index: 8;
		display: flex;
		align-items: center;
		flex-direction: column;
	}
	.vodMenu{
		margin-top: 100rpx;
		width: 115rpx;
		/* bottom: 400rpx; */
		display: flex;
		align-items: center;
		flex-direction: column;
	}
	.vodMenu-bright0{
		opacity: 1;
		transition: all 0.3s linear;
	}
	.vodMenu-bright1{
		opacity: 0;
		transition: all 0.3s linear;
	}
	.vodMenu-bright2{
		opacity: 0.2;
		transition: all 0.3s linear;
	}
	.menu-avatar{
		position: relative;
		width: 115rpx;
		height: 115rpx;
		border-radius: 50%;
		background-color: #FFFFFF;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.avatar-image{
		width: 100rpx;
		height: 100rpx;
		border-radius: 50%;
	}
	.follow{
		position: absolute;
		bottom: -20rpx;
		width: 40rpx;
		height: 40rpx;
		background-color: #FFFFFF;
		border-radius: 50%;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.follow:active{
		transform: scale(0.7);
		transition: all 0.3s linear;
	}
	.follow-guanzhu{
		width: 40rpx;
		height: 40rpx;
	}
	.guanzhu-gou{
		width: 30rpx;
		height: 30rpx;
	}
	.followActive{
		transform: rotate(180deg);
		transition: all 0.5s linear;
	}
	.followHide{
		transform: scale(0);
		transition: all 0.8s linear;
	}
	.fabulous{
		width: 100rpx;
		margin-top: 80rpx;
		display: flex;
		align-items: center;
		flex-direction: column;
	}
	.fabulous-image{
		width: 80rpx;
		height: 80rpx;
	}
	.fabulous-num{
		width: 100rpx;
		font-size: 28rpx;
		color: #FFFFFF;
		text-align: center;
		margin-top: 10rpx;
	}
	.rotate-avatar{
		width: 95rpx;
		height: 95rpx;
		border-radius: 95rpx;
		background-color: #333333;
		display: flex;
		align-items: center;
		justify-content: center;
		animation: rotateName 5s linear infinite;
	}
	.rotate-image{
		width: 55rpx;
		height: 55rpx;
		border-radius: 55rpx;
	}
	@keyframes rotateName{
		0%{
			transform: rotate(0deg);
		}
		100%{
			transform: rotate(360deg);
		}
	}
	.like-pellet{
		width: 5rpx;
		height: 5rpx;
		background-color: transparent;
		border-radius: 50%;
		/* 绝对定位 居中 */
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%,-50%) scale(0);
		/* 设置各个方向的阴影 */
		box-shadow: 0 -45rpx 0 #FF0000,
		0 48rpx 0 #FF0000,
		-50rpx 0 0 #FF0000,
		50rpx 0 0 #FF0000,
		-35rpx -35rpx 0 #FF0000,
		35rpx -35rpx 0 #FF0000,
		35rpx 35rpx 0 #FF0000,
		-35rpx 35rpx 0 #FF0000;
	}
	.like-pellet-active{
		animation: likeBlink 0.5s ease-in-out forwards;
	}
	/* 小圆点闪出的动画 */
	@keyframes likeBlink {
	    0%{
	        transform: translate(-50%,-50%) scale(0.5);
	        opacity: 0.8;
	    }
	    50%{
	        transform: translate(-50%,-50%) scale(1);
	        opacity: 1;
	    }
	    100%{
	        transform: translate(-50%,-50%) scale(1.1);
	        opacity: 0;
	    }
	}
	
	/* 底部标题部分 */
	.footTitle{
		position: absolute;
		bottom: 20px;
		left: 0;
		z-index: 8;
		width: 500rpx;
		margin-left: 30rpx;
	}
	.foot-name{
		font-size: 33rpx;
		color: #FFFFFF;
		font-weight: bold;
	}
	.foot-cont{
		font-size: 30rpx;
		color: #FFFFFF;
	}
	.foot-primary{
		margin-top: 10rpx;
		font-size: 25rpx;
		color: #FFFFFF;
	}
	.k-paused{
		animation-play-state: paused;
	}
	.k-running{
		animation-play-state: running;
	}
	.fabulous-taoxin{
		animation: likeName 0.5s linear 1;
	}
	@keyframes likeName{
		0%{
			transform: scale(1);
		}
		15%{
			transform: scale(0);
		}
		30%{
			transform: scale(0.5);
		}
		50%{
			transform: scale(1);
		}
		70%{
			transform: scale(1.2);
		}
		80%{
			transform: scale(1);
		}
		90%{
			transform: scale(1.1);
		}
		100%{
			transform: scale(1);
		}
	}
	.cancel-taoxin{
		animation: likeCancel 0.2s linear 1;
	}
	@keyframes likeCancel{
		0%{
			transform: scale(1);
		}
		50%{
			transform: scale(0.7);
		}
		100%{
			transform: scale(1);
		}
	}
</style>