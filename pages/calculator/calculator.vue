<template>
	<view>
		<official-account></official-account>
		<uni-group top="10">
			<qiun-title-bar title="请选择要计算的目标"/>
			<view class="content">
					<vgt-tab :list="list" :isUseOpenList=true @onValueChange="onValueChange" > </vgt-tab>
			</view>
			<view class="example">
				<uni-notice-bar single="true" text="注意:年化收益率采用实际利率, 而非名义利率!"></uni-notice-bar>
				<uni-forms labelWidth='90' ref="baseForm" :rules="rules" :modelValue="baseFormData">
					<uni-forms-item label="初始金额" name="start_amount" v-if="tabChoose.currentItem != '初始金额'">
						<uni-easyinput v-model="baseFormData.start_amount" placeholder="请输入大于等于0的数" />
					</uni-forms-item>
					<uni-forms-item label="投资年限" name='year' v-if="tabChoose.currentItem != '投资年限'">
						<uni-easyinput v-model="baseFormData.year" placeholder="1-100之间" />
					</uni-forms-item>
					<uni-forms-item label="年化收益率" name='irr' v-if="tabChoose.currentItem != '年化收益率'">
						<uni-easyinput v-model="baseFormData.irr" placeholder="收益率单位默认 %" />
					</uni-forms-item>
					<uni-forms-item label="定投频率" labelWidth='80'>
						<uni-data-checkbox v-model="baseFormData.frequently" :localdata="frequent" />
					</uni-forms-item>
					<uni-forms-item label="定投金额" name='cash_flow' v-if="tabChoose.currentItem != '定投金额'">
						<uni-easyinput v-model="baseFormData.cash_flow" placeholder="请输入大于等于0的数" />
					</uni-forms-item>
					<uni-forms-item label="期末资产" name='end_amount' v-if="tabChoose.currentItem != '期末资产'">
						<uni-easyinput v-model="baseFormData.end_amount" placeholder="请输入大于等于0的数" />
					</uni-forms-item>
					<button type="primary" @click="submit('baseForm')">提交</button>
				</uni-forms>
			</view>
		</uni-group>
		<uni-group top="10" v-show="display">
		<!-- <uni-group top="10" v-show="true"> -->
			<qiun-title-bar title="计算结果"/>
			<p style="text-align:center;color:#000000;font-size: 24px;margin-bottom: 8px;">{{tabChoose.currentItem}}</p>
			<p style="text-align:center;color:#ff0000;font-size: 24px;margin-bottom: 8px;">{{outputRes[tabChoose.currentItem]}}</p>
			<view class="example">
				<uni-list>
					<uni-list-item  title="初始金额" v-bind:rightText="String(investRes.start_amount)+' 元'"></uni-list-item>
					<uni-list-item  title="投资年限" v-bind:rightText="String(investRes.year)+' 年'"></uni-list-item>
					<uni-list-item  title="年化收益率" v-bind:rightText="String((investRes.irr*100)+'%')"></uni-list-item>
					<uni-list-item  title="定投金额" v-bind:rightText="String(investRes.cash_flow)+' 元'"></uni-list-item>
					<uni-list-item  title="投入本金" v-bind:rightText="String(investRes.total_contribute)+' 元'"></uni-list-item>
					<uni-list-item  title="总收益" v-bind:rightText="String(investRes.interest)+' 元'"></uni-list-item>
					<uni-list-item  title="期末资产" v-bind:rightText="String(investRes.end_amount)+' 元'"></uni-list-item>
				</uni-list>
			</view>
		</uni-group>
		<uni-group top="10" v-show="display">
			<qiun-title-bar title="收益曲线"/>
			<view class="example">
				<view class="charts-box" reshow=true>
				  <qiun-data-charts type="area" :opts="{dataLabel:false,enableScrollL:true, xAxis:{labelCount:10}, extra:{area:{type:'curve'}}}" :chartData="chartsDataArea"/>
				</view>
			</view>
		</uni-group>
		<uni-group top="10" v-show="display">
			<qiun-title-bar title="收益占比"/>
			<view class="charts-box" reshow=true>
			  <qiun-data-charts type="pie" :chartData="chartsDataPie"/>
			</view>
		</uni-group>
	</view>
</template>

<script>
	import vgtTab from '@/components/vgt-tab/vgt-tab.vue'
	export default {
		components: {
		    'vgt-tab': vgtTab
		},
		data() {
			return {
				// 选项卡
				list: [
					'期末资产',
					'年化收益率',
					'定投金额',
					'初始金额',
					'投资年限'
				],
				tabChoose: {
					"currentIndex": 0,
					"currentItem": "期末资产"
				},
				// 基础表单数据 
				baseFormData: {
					start_amount: '',
					year: '',
					irr: '',
					frequently: 'm',
					cash_flow: '',
					end_amount: '',
					cal_type: ""
				},
				// 单选数据源
				frequent: [{
					text: '每周定投',
					value: 'w'
				}, {
					text: '每月定投',
					value: 'm'
				}, {
					text: '每年定投',
					value: 'y'
				}],
				// 校验规则
				rules: {
					start_amount: {
						rules: [{
							required: true,
							errorMessage: '初始金额不能为空'
						}, {
							format: 'number',
							errorMessage: '初始金额必须是数字'
						}, {
							min: 0,
							errorMessage: '初始金额须大于等于0'
						}]
					},
					year: {
						rules: [{
							required: true,
							errorMessage: '投资年限不能为空'
						}, {
							format: 'number',
							errorMessage: '投资年限只能是数字'
						}, {
							minimum: 1,
							maximum: 100,
							errorMessage: '投资年限须在1-100之间'
						}]
					},
					irr: {
						rules: [{
							required: true,
							errorMessage: '年化收益率不能为空'
						}, {
							format: 'number',
							errorMessage: '年化收益率只能是数字'
						}, {
							minimum: 0,
							maximum: 100,
							errorMessage: '请输入0-100之间的数'
						}]
					},
					cash_flow: {
						rules: [{
							required: true,
							errorMessage: '定投金额不能为空'
						}, {
							format: 'number',
							errorMessage: '定投金额必须是数字'
						}, {
							min: 0,
							errorMessage: '定投金额须大于等于0'
						}]
					},
					end_amount: {
						rules: [{
							required: true,
							errorMessage: '期末资产不能为空'
						}, {
							format: 'number',
							errorMessage: '期末资产必须是数字'
						}, {
							min: 0,
							errorMessage: '期末资产须大于等于0'
						}]
					},
				},
				display: false,
				chartsDataArea: {},
				chartsDataPie: {},
				investRes: {},
				outputRes: {}
			}
		},
		onLoad() {},
		onShareAppMessage(res) {
		    if (res.from === 'button') {// 来自页面内分享按钮
		      console.log(res.target)
		    }
		    return {
		      title: '投资工具箱——定投计算器',
		      path: '/pages/calculator/calculator'
		    }
		},
		methods: {
			onValueChange: function(e){
				this.$refs.baseForm.resetFields(this.baseFormData);
				this.display = false;
				console.log(e);
				this.tabChoose = e
				if (e.currentItem == "期末资产") {
					this.baseFormData.cal_type = "end_amount_cal"
				} else if (e.currentItem == "年化收益率") {
					this.baseFormData.cal_type = "irr_cal"
				} else if (e.currentItem == "初始金额") {
					this.baseFormData.cal_type = "start_amount_cal"
				} else if (e.currentItem == "投资年限") {
					this.baseFormData.cal_type = "year_cal"
				} else if (e.currentItem == "定投金额") {
					this.baseFormData.cal_type = "cash_flow_cal"
				}
			},
			submit(ref) {
				this.$refs[ref].validate().then(result => {
					uni.request({
						url: 'https://www.afreecoder.com.cn/proxy',
						header: {
							"X-Upstream-Url": "http://127.0.0.1:8000/calculator/investment/fixed"
						},
						method: 'GET',
						data: this.baseFormData,
						success: (res) => {
							if (res.statusCode != 200) {
								uni.showToast({
									icon: 'error',
									title: `请求异常`
								})
								this.display = false
							} else {
								this.investRes = res.data.invest_res
								this.chartsDataArea = {
									"categories": res.data.cordinate.x,
									"series": [{
										"name": "投入本金",
										"data": res.data.cordinate.y_contribute
									}, {
										"name": "期末资产",
										"data": res.data.cordinate.y_total
									}]
								}
								this.chartsDataPie = {
									"series": [{
										"data": [
										{
											"name": "投入本金",
											"value": this.investRes.total_contribute
										}, {
											"name": "收益",
											"value": this.investRes.interest
										}
									  ]
									}]
								 }
								this.display = true
								this.outputRes = {
									"初始金额": String(this.investRes.start_amount)+' 元',
									"投资年限": String(this.investRes.year)+' 年',
									"投入本金": String(this.investRes.total_contribute)+' 元',
									"总收益": String(this.investRes.interest)+' 元',
									"期末资产": String(this.investRes.end_amount)+' 元',
									"年化收益率": String(this.investRes.irr*100)+'%',
									"定投金额": String(this.investRes.cash_flow)+' 元',
								}
							}
						},
					})
				}).catch(err => {
					console.log('err', err);
				})
			}
		}
	}
</script>

<style lang="scss">
	@import '@/common/uni-nvue.scss';

	.example {
		padding: 15px;
		background-color: #fff;
	}

	.segmented-control {
		margin-bottom: 15px;
	}

	.button-group {
		margin-top: 15px;
		display: flex;
		justify-content: space-around;
	}

	.form-item {
		display: flex;
		align-items: center;
	}

	.button {
		display: flex;
		align-items: center;
		height: 35px;
		margin-left: 10px;
	}
</style>
