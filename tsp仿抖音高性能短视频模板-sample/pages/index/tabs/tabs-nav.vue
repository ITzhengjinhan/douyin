<template>
	<view>
		<scroll-view ref="tabbar1" id="tab-bar" class="tab-bar" :scroll="false" :scroll-x="true" :show-scrollbar="false"
		  :scroll-into-view="scrollInto" :scroll-with-animation="true">
		  <view style="flex-direction: column;">
		    <view style="flex-direction: row;">
		      <view class="uni-tab-item" v-for="(tab,index) in tabList" :key="tab.id" :id="tab.id" :ref="'tabitem'+index"
		        :data-id="index" :data-current="index" @click="ontabtap(index)">
		        <text class="uni-tab-item-title" :class="tabIndex==index ? 'uni-tab-item-title-active' : ''">{{tab.name}}</text>
		      </view>
		    </view>
		    <view class="scroll-view-indicator">
		      <view ref="underline" class="scroll-view-underline" :class="isTap ? 'scroll-view-animation':''" :style="{left: indicatorLineLeft + 'px', width: indicatorLineWidth + 'px'}"></view>
		    </view>
		  </view>
		</scroll-view>
	</view>
</template>

<script>
	// #ifdef APP-PLUS
	const dom = weex.requireModule('dom');
	// #endif
	
	export default{
		props:{
			tabList:{
				type:Array,
				default:()=>{ return [] }
			},
			swiperWidth:{
				type:Number,
				default:0
			},
			value:{
				type:Number,
				default:0
			},
			tabWidth:{
				
			}
		},
		data(){
			return {
				tabIndex: this.value,
				scrollInto: "",
				indicatorLineLeft: 0,
				indicatorLineWidth: 0,
				isTap: false,
				tabListSize:[],
				tabbarWidth:0,
				lastTabIndex:0,
				touchTabIndex:0
			}
		},
		created() {
			setTimeout(()=>{
				if(this.tabList.length > 0){
					this.selectorQuery()
				}
			},300)
		},
		methods:{
			ontabtap(index){
				this.isTap = true;
				this.touchTabIndex = index
				let currentSize = this.tabListSize[index];
				this.updateIndicator(currentSize.left, currentSize.width);
				this.switchTab(index)
			},
			selectorQuery() {
			  this.$nextTick(()=>{
				  // #ifdef APP-NVUE
				  dom.getComponentRect(this.$refs.tabbar1, res => {
				    this.tabbarWidth = res.size.width;
				  });
				  let promises = [];
				  this.tabList.forEach((item,index)=>{
					  const promise = new Promise((resolve) => {
							const el = this.$refs['tabitem' + index][0];
							dom.getComponentRect(el, res => {
								this.tabListSize.push(res.size)
								resolve();
							});
					  });
					  promises.push(promise);
				  })
				  Promise.all(promises).then(() => {
					  // console.log('循环已经结束1',this.tabListSize);
					  this.updateIndicator(this.tabListSize[this.tabIndex].left, this.tabListSize[this.tabIndex].width);
				  });
				  // #endif
				  
				  // #ifndef APP-NVUE
				  uni.createSelectorQuery().in(this).selectAll('.uni-tab-item').boundingClientRect((rects) => {
				    rects.forEach((rect) => {
						this.tabListSize.push(rect)
				    })
				    this.updateIndicator(this.tabListSize[this.tabIndex].left, this.tabListSize[this.tabIndex].width);
				  }).exec();
				  // #endif
			  })
			},
			onswiperscroll(e) {
			  if (this.isTap) return;
			  let offsetX = e.detail.dx;
			  let preloadIndex = this.lastTabIndex;
			  if (offsetX > 1) {
			    preloadIndex++;
			  } else if (offsetX < -1) {
			    preloadIndex--;
			  }
			  if (preloadIndex === this.lastTabIndex || preloadIndex < 0 || preloadIndex > this.tabList.length - 1) return;
			  
			  let percentage = Math.abs(this.swiperWidth / offsetX);
			  let currentSize = this.tabListSize[this.lastTabIndex];
			  let preloadSize = this.tabListSize[preloadIndex];
			  let lineL = currentSize.left + (preloadSize.left - currentSize.left) / percentage;
			  let lineW = currentSize.width + (preloadSize.width - currentSize.width) / percentage;
			  this.updateIndicator(lineL, lineW);
			},
			animationfinish(e) {
			  let index = e.detail.current;
			  if (this.touchTabIndex === index) {
			    this.isTap = false;
			  }
			  this.lastTabIndex = index;
			  this.switchTab(index);
			  this.updateIndicator(this.tabListSize[index].left, this.tabListSize[index].width);
			},
			updateIndicator(left, width) {
			  this.$nextTick(()=>{
				  this.indicatorLineLeft = left;
				  this.indicatorLineWidth = width;
			  })
			},
			switchTab(index) {
			  if (this.tabIndex === index) return;
			  this.tabIndex = index;
			  // #ifdef APP-NVUE
			  this.scrollTabTo(index);
			  // #endif
			  // #ifndef APP-NVUE
			  this.scrollInto = this.tabList[index].id;
			  // #endif
			   this.$emit("input", this.tabIndex);
			   this.$emit("changeSwitchTab", this.tabIndex);
			},
			scrollTabTo(index) {
			  const el = this.$refs['tabitem' + index][0];
			  let offset = 0;
			  // TODO fix ios offset
			  if (index > 0) {
			    offset = this.tabbarWidth / 2 - this.tabListSize[index].width / 2;
			    if (this.tabListSize[index].right < this.tabbarWidth / 2) {
			      offset = this.tabListSize[0].width;
			    }
			  }
			  dom.scrollToElement(el, {
			    offset: -offset
			  });
			},
		}
	}
</script>

<style>
	.tab-bar {
	  /* #ifdef APP-PLUS */
	  width: 750rpx;
	  /* #endif */
	  height: 42px;
	  flex-direction: row;
	  /* #ifndef APP-PLUS */
	  white-space: nowrap;
	  /* #endif */
	}
	
	/* #ifndef APP-NVUE */
	.tab-bar ::-webkit-scrollbar {
	  display: none;
	  width: 0 !important;
	  height: 0 !important;
	  -webkit-appearance: none;
	  background: transparent;
	}
	
	/* #endif */
	
	.scroll-view-indicator {
	  position: relative;
	  height: 2px;
	  background-color: transparent;
	}
	
	.scroll-view-underline {
	  position: absolute;
	  top: 0;
	  bottom: 0;
	  width: 0;
	  background-color: #007AFF;
	}
	
	.scroll-view-animation {
	  transition-duration: 0.2s;
	  transition-property: left;
	}
	
	.tab-bar-line {
	  height: 1px;
	  background-color: #cccccc;
	}
	
	.uni-tab-item {
	  /* #ifndef APP-PLUS */
	  display: inline-block;
	  /* #endif */
	  flex-wrap: nowrap;
	  padding-left: 20px;
	  padding-right: 20px;
	}
	
	.uni-tab-item-title {
	  color: #555;
	  font-size: 15px;
	  height: 40px;
	  line-height: 40px;
	  flex-wrap: nowrap;
	  /* #ifndef APP-PLUS */
	  white-space: nowrap;
	  /* #endif */
	}
	
	.uni-tab-item-title-active {
	  color: #007AFF;
	}
</style>