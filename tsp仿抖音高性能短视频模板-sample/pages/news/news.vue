<template>
	<view>
		<view style="margin-top: 200rpx;">消息</view>
	</view>
</template>

<script>
	export default{
		data(){
			return {
				
			}
		},
		onShow() {
			const deviceInfo = uni.getSystemInfoSync()
			console.log(deviceInfo)
		}
	}
</script>

<style>
</style>
