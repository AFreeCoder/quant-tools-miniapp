<template>
	<view class="content">
		<qiun-title-bar title="集思录温度计"/>
		<!-- 这里的title.name和subtitle.name如果需要联动chartData，请定义一个变量同步传入:opts和:chartData中 -->
		<view class="charts-box">
		  <qiun-data-charts type="gauge" :opts="{title:{name: temperature,color: '#2fc25b',fontSize: 25,offsetY:50},subtitle: {name: '当前温度',color: '#666666',fontSize: 15,offsetY:-50}}" :chartData="chartsDataGauge1"/>
		</view>
		<qiun-title-bar title="蛋卷估值"/>
		<!-- 这里的title.name和subtitle.name如果需要联动chartData，请定义一个变量同步传入:opts和:chartData中 -->
		<!-- <view class="charts-box"> -->
			<uni-table border stripe emptyText="暂无更多数据" >
			    <!-- 表头行 -->
			    <uni-tr>
			        <uni-th align="center" width="100">指数名称</uni-th>
			        <uni-th align="center" width="100">指数类型</uni-th>
			        <uni-th align="center" width="50">pe</uni-th>
					<uni-th align="center" width="100">pe百分位</uni-th>
					<uni-th align="center" width="50">pb</uni-th>
					<uni-th align="center" width="100">pb百分位</uni-th>
					<uni-th align="center" width="75">股息率</uni-th>
					<uni-th align="center" width="50">roe</uni-th>
					<uni-th align="center" width="50">预测peg</uni-th>
			    </uni-tr>
			    <!-- 表格数据行 -->
			    <uni-tr v-for="item in index_eva">
			        <uni-td><view :style="'color:'+eva_level[item.eva_type]">{{item.name}}</view></uni-td>
			        <uni-td>{{index_type[item.ttype]}}</uni-td>
			        <uni-td>{{item.pe.toFixed(2)}}</uni-td>
					<uni-td>{{(item.pe_percentile*100).toFixed(2)}}%</uni-td>
					<uni-td>{{item.pb.toFixed(2)}}</uni-td>
					<uni-td>{{(item.pb_percentile*100).toFixed(2)}}%</uni-td>
					<uni-td>{{(item.yeild*100).toFixed(2)}}%</uni-td>
					<uni-td>{{(item.roe*100).toFixed(2)}}%</uni-td>
					<uni-td>{{item.peg}}</uni-td>
			    </uni-tr>
			</uni-table>
			</script>
		<!-- </view> -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				color_type: "red",
				index_type: {
					1: "沪深300",
					2: "策略指数",
					3: "行业指数"
				},
				eva_level: {
					"low": "green",
					"mid": "#e48f0a",
					"high": "red"
				},
				chartsDataGauge1:{},
				temperature: 0,
				index_eva: {},
				name: ''
			}
		},
		onLoad() {
		  //从服务器获取数据
		  this.getServerData()
		},
		methods: {
			getServerData() {
				uni.request({
					url: 'https://www.afreecoder.com.cn/proxy',
					header: {
						"X-Upstream-Url": "https://www.jisilu.cn/data/indicator/get_last_indicator/"
					},
					method: 'GET',
					success: (res) => {
						this.chartsDataGauge1 = {
							"categories": [{
								"value": 0.3,
								"color": "#18bd04"
							}, {
								"value": 0.7,
								"color": "#ffe24d"
							}, {
								"value": 1,
								"color": "#FF0000"
							}],
							"series": [{
								"name": "温度值",
								"data": res.data.median_pb_temperature / 100
							}]
						};
						this.temperature = res.data.median_pb_temperature;
					}
				})
				uni.request({
					url: 'https://www.afreecoder.com.cn/proxy',
					header: {
						"X-Upstream-Url": "https://danjuanapp.com/djapi/index_eva/dj"
					},
					method: 'GET',
					success: (res) => {
						this.index_eva = res.data.data.items;
					}
				})
			}
		}
	}
</script>

<style>
.content {
  display: flex;
  flex-direction: column;
  flex: 1;
}

.charts-box {
  width: 100%;
  height: 350px;
}
</style>