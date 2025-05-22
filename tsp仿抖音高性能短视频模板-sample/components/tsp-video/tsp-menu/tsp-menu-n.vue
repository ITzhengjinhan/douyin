<template>
	<view>
		<!-- 底部标题 -->
		<view @click.stop.prevent="moveHandle" class="footTitle" :class="[vodIndex == index?(sliderDrag?'vodMenu-bright1':(moveOpacity?'vodMenu-bright2':'vodMenu-bright0')):'']">
			<view><text class="foot-name">@卧槽无情</text></view>
			<view><text class="foot-cont">开发不易，如果对你有帮助，请给个五星好评！</text></view>
			<view style="width: 300rpx;">
				<text class="foot-primary">卧槽无情的原声-卧槽</text>
			</view>
		</view>
		<!-- 右侧操作栏 -->
		<view class="menuBox" :style="{height:vodHeight+'px'}" @click.stop.prevent="moveHandle">
			<view class="vodMenu" :class="[vodIndex == index?(sliderDrag?'vodMenu-bright1':(moveOpacity?'vodMenu-bright2':'vodMenu-bright0')):'']">
				<!-- 头像 -->
				<view class="vodMenu-top">
					<view class="menu-avatar" @click="JumpBtn(1)">
						<image src="/static/icon/touxiang.jpg" mode="" class="avatar-image"></image>
					</view>
					<view class="follow" @click="followBtn(index)" v-if="!item.followReally" :class="{followHide:followShow==2}">
						<image src="/static/icon/gou.png" mode="" class="follow-guanzhu guanzhu-gou" v-if="followShow == 1 || followShow == 2"></image>
						<image src="/static/icon/guanzhu.png" mode="" class="follow-guanzhu" v-if="followShow==null || followShow==0"></image>
					</view>
				</view>
				<!-- 点赞 -->
				<view class="fabulous" @click="JumpBtn(2)" style="position: relative;padding-top: 5rpx;">
					<view class="likeBox" :ref="'pelRef'+index">
						<view class="like-pellet" :class="'like-pellet'+j" v-for="(lt,j) in 8" :key="j"></view>
					</view>
					<view class="fabulous-image" @click="fabulousBtn" :ref="'likeRef'+index">
						<image src="/static/icon/selectTaoxin.png" mode="" class="fabulous-image" v-if="item.fabulousShow"></image>
						<image src="/static/icon/taoxin.png" mode="" class="fabulous-image" v-else></image>
					</view>
					<text class="fabulous-num">{{vodIndex}}</text>
				</view>
				<!-- 评论 -->
				<view class="fabulous" style="margin-top: 30rpx;" @click="JumpBtn(3)">
					<view class="fabulous-image">
						<image src="/static/icon/pinlun.png" mode="" class="fabulous-image"></image>
					</view>
					<text class="fabulous-num">{{discussNum}}</text>
				</view>
				<!-- 转发 -->
				<view class="fabulous" style="margin-top: 30rpx;" @click="JumpBtn(4)">
					<view class="fabulous-image">
						<image src="/static/icon/ward.png" mode="" class="fabulous-image"></image>
					</view>
					<text class="fabulous-num" style="font-size: 26rpx;">转发</text>
				</view>
			</view>
		</view>
		<!-- 旋转头像 -->
		<view @click.stop.prevent="moveHandle" class="avatarMenu" v-if="item.rotateImgShow" :class="[vodIndex == index?(sliderDrag?'vodMenu-bright1':(moveOpacity?'vodMenu-bright2':'vodMenu-bright0')):'']">
			<view style="position: relative;width: 95rpx;height: 95rpx;" @click="JumpBtn(5)">
				<view :ref="'rotateImg'+ index">
					<view class="rotate-avatar">
						<image src="/static/icon/touxiang.jpg" mode="" class="rotate-image"></image>
					</view>
				</view>
				<view :style="`position: absolute;top: 0;left: 0;opacity:${item.vodPaly? 0 : 1}`">
					<view class="rotate-avatar">
						<image src="/static/icon/touxiang.jpg" mode="" class="rotate-image"></image>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	const animation = uni.requireNativePlugin('animation')
	export default{
		props:{
			//视频部分的高度
			vodHeight:{ 
				type:Number,
				default:0
			},
			//下标索引
			index:{
				type:Number,
				default:0
			},
			//当前播放的视频下标
			vodIndex:{
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
		},
		data(){
			return {
				followShow:null,
				fabuTimeOut:null,
				likeNum:0,
				pelletNum:0,
				/* 点赞动画 */
				likeDropList:{
					list:[
						{transform:'scale(1)'},
						{transform:'scale(0)'},
						{transform:'scale(0.5)'},
						{transform:'scale(1)'},
						{transform:'scale(1.2)'},
						{transform:'scale(1)'},
						{transform:'scale(1.1)'},
						{transform:'scale(1)'}
					],
					duration:62
				},
				/* 取消点赞动画 */
				likeCancelList:{
					list:[
						{transform:'scale(1)'},
						{transform:'scale(0.7)'},
						{transform:'scale(1)'}
					],
					duration:66
				},
				/* 小圆点闪出的动画 */
				pelletList:{
					list:[
						{transform:'scale(0.5)',opacity: '0.8'},
						{transform:'scale(1)',opacity: '1'},
						{transform:'scale(1.1)',opacity: '0'}
					],
					duration:166
				}
			}
		},
		methods:{
			/* 阻止事件冒泡 */
			moveHandle(event){
				event.stopPropagation()
			},
			/* 旋转头像动画 */
			rotateAvatar(){
				let testEl = this.$refs['rotateImg'+this.vodIndex]
				animation.transition(testEl, {
				  styles: {
					  transform:'rotate(0deg)',
				  },
				  duration: 0, //ms
				  timingFunction: 'linear',
				  delay: 0 //ms
				},()=>{
					this.rotateAvatar1();
				})
			},
			rotateAvatar1(){
				let testEl = this.$refs['rotateImg'+this.vodIndex]
				animation.transition(testEl, {
				  styles: {
					  transform:'rotate(360deg)',
				  },
				  duration: 3000, //ms
				  timingFunction: 'linear',
				  delay: 0 //ms
				},()=>{
					this.rotateAvatar();
				})
			},
			/* 视频点赞动效 */
			fabulousBtn(){
				let obj = Object.assign({},this.item)
				obj.fabulousShow = !obj.fabulousShow
				this.likeNum = 0
				this.pelletNum = 0
				let result = obj.fabulousShow ? this.likeDropList : this.likeCancelList
				this.addAnimation('likeRef',result,this.likeNum) //点赞动画
				if(obj.fabulousShow){ //小圆点闪出动画
					this.addAnimation('pelRef',this.pelletList,this.pelletNum)
				}
				this.$emit('fabulousBtn',{obj:obj,index:this.index})  //点赞成功
				/* clearTimeout(this.fabuTimeOut)
				this.fabuTimeOut = setTimeout(()=>{
					console.log('发送请求')
				},300) */
			},
			/* 关注动效 */
			followBtn(index){
				let obj = Object.assign({},this.item)
				obj.followReally = true
				this.followShow = 0
				setTimeout(()=>{
					this.followShow = 1
					setTimeout(()=>{
						this.followShow = 2
						setTimeout(()=>{
							this.$emit('fabulousBtn',{obj:obj,index:this.index})  //关注成功
						},500)
					},50)
				},300)
			},
			/* 点击右侧菜单选项 1头像 2点赞 3评论 4转发 5旋转头像 */
			JumpBtn(index){
				switch(index){
					case 1 :
						console.log('点击头像')
						uni.navigateTo({
							url:'/pages/details/details-n'
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
			},
			/* 动画方法 */
			addAnimation(name,dataList,num){
				let testEl = this.$refs[name+this.vodIndex]
				animation.transition(testEl, {
				  styles: dataList.list[num],
				  duration: dataList.duration, //ms
				  timingFunction: 'linear',
				  delay: 0 //ms
				},()=>{
					num = num + 1
					if(num < dataList.list.length){
						this.addAnimation(name,dataList,num);
					}
				})
			}
		}
	}
</script>

<style>
	.menuBox{
		position: absolute;
		right: 10rpx;
		width: 115rpx;
		z-index: 8;
		justify-content: center;
	}
	.avatarMenu{
		position: absolute;
		bottom: 20px;
		right: 10rpx;
		width: 115rpx;
		z-index: 8;
		align-items: center;
		flex-direction: column;
	}
	.vodMenu{
		margin-top: 100rpx;
		width: 115rpx;
		width: 115rpx;
		align-items: center;
		flex-direction: column;
	}
	.vodMenu-bright0{
		opacity: 1;
		transition-property: opacity;
		transition-timing-function:linear;
		transition-duration: 0.3s;
	}
	.vodMenu-bright1{
		opacity: 0;
		transition-property: opacity;
		transition-timing-function:linear;
		transition-duration: 0.3s;
	}
	.vodMenu-bright2{
		opacity: 0.2;
		transition-property: opacity;
		transition-timing-function:linear;
		transition-duration: 0.3s;
	}
	.vodMenu-top{
		height: 140rpx;
		width: 115rpx;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}
	.menu-avatar{
		position: relative;
		width: 115rpx;
		height: 115rpx;
		border-radius: 115rpx;
		background-color: #FFFFFF;
		align-items: center;
		justify-content: center;
	}
	.avatar-image{
		width: 100rpx;
		height: 100rpx;
		border-radius: 100rpx;
	}
	.follow{
		position: absolute;
		bottom: 0;
		width: 40rpx;
		height: 40rpx;
		background-color: #FFFFFF;
		border-radius: 40rpx;
		align-items: center;
		justify-content: center;
	}
	.follow:active{
		transition-property: transform;
		transition-timing-function:linear;
		transition-duration: 0.4s;
		transform: scale(0.7);
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
		transition-property: transform;
		transition-timing-function:linear;
		transition-duration: 0.5s;
		transform: rotate(180deg);
	}
	.followHide{
		transition-property: transform;
		transition-timing-function:linear;
		transition-duration: 0.5s;
		transform: scale(0.1);
	}
	.fabulous{
		width: 100rpx;
		margin-top: 80rpx;
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
	.likeBox{
		position: absolute;
		width: 100rpx;
		height: 94rpx;
		opacity: 0;
		transform: scale(0.1);
	}
	.like-pellet{
		width: 5rpx;
		height: 5rpx;
		background-color: #FF0000;
		border-radius: 5rpx;
		position: absolute;
	}
	.like-pellet0{
		top: 10rpx;
		left: 5rpx;
	}
	.like-pellet1{
		top: 10rpx;
		right: 5rpx;
	}
	.like-pellet2{
		bottom: 13rpx;
		left: 13rpx;
	}
	.like-pellet3{
		bottom: 13rpx;
		right: 13rpx;
	}
	.like-pellet4{
		top: 0;
		left: 46rpx;
	}
	.like-pellet5{
		bottom: 2rpx;
		left: 46rpx;
	}
	.like-pellet6{
		left: 0;
		top: 44rpx;
	}
	.like-pellet7{
		right: 0;
		top: 44rpx;
	}
	/* 底部标题部分 */
	.footTitle{
		position: absolute;
		bottom: 20px;
		left: 0;
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
	.rotate-avatar{
		width: 95rpx;
		height: 95rpx;
		border-radius: 95rpx;
		background-color: #333333;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.rotate-image{
		width: 55rpx;
		height: 55rpx;
		border-radius: 55rpx;
	}
</style>