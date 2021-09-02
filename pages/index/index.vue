<template>
	<view>
		<official-account></official-account>
		<uni-group mode="card">
			<view style="display: flex;">
				<image mode="widthFix" style="vertical-align: middle;width: 750rpx;" src="../../static/index/banner_1.png"/>
			</view>
		</uni-group>

		<uni-group mode="card">
			<view class="container">
				<view class="grid-template-columns">
					<navigator url="/pages/calculator/calculator" open-type="navigate"
						hover-class="other-navigator-hover">
						<image src="../../static/index/calculator.png" style="vertical-align:middle;width: 80rpx;height: 80rpx;"><text style="margin-left:24rpx;font-size:32rpx;" >定投计算器</text>
					</navigator>
				</view>
				<view class="grid-template-columns">
					<navigator url="/pages/market/market" open-type="navigate"
						hover-class="other-navigator-hover">
						<image src="../../static/index/temperature.png" style="vertical-align:middle;width: 80rpx;height: 80rpx;"><text style="margin-left:24rpx;font-size:32rpx;" >估值温度计</text>
					</navigator>
				</view>
				<view class="grid-template-columns">
					<image src="../../static/index/lock.png" style="vertical-align:middle;width: 80rpx;height: 80rpx;"><text style="margin-left:24rpx;font-size:32rpx;">待解锁</i></text>
				</view>
				<view class="grid-template-columns">
					<image src="../../static/index/lock.png" style="vertical-align:middle;width: 80rpx;height: 80rpx;"><text style="margin-left:24rpx;font-size:32rpx;">待解锁</i></text>
				</view>
			</view>	
		</uni-group>
		<view style="text-align: center;">
			<text style="color: #808080;">更多功能，敬请期待！</text>
		</view>
	</view>
</template>

<script>
	export default {
		data () {
			return {}
		},
		onLoad() {},
		methods: {}
	}
</script>

<style lang="scss">
	@import '@/common/uni-nvue.scss';

	.text {
		font-size: 26rpx;
		margin-top: 10rpx;
	}
	
	.container {
	  display: grid;
	  grid-template-columns: 1fr 1fr;
	  height: 250rpx;
	  background-color: #fdfffb;
	 }

</style>