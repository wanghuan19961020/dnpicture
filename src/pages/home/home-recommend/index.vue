<template>
	<scroll-view @scrolltolower="handleToLower" class="recommend-view" scroll-y v-if="recommends.length > 0">
		<!-- 推荐 开始 -->
		<view class="recommend-wrap">
			<navigator class="recommend-item" v-for="item in recommends" :key="item.id" :url="`/pages/album/index?id=${item.target}`">
				<image :src="item.thumb" mode="widthFix" />
			</navigator>
		</view>
		<!-- 推荐 结束 -->
		<!-- 月份 开始 -->
		<view class="months-wrap">
			<view class="months-title">
				<view class="months-title-info">
					<view class="months-info">
						<text> {{months.DD}} / </text>
						{{months.MM}} 月
					</view>
					<view class="months-text">
						{{months.title}}
					</view>
				</view>
				<view class="months-title-more">
					更多 >
				</view>
			</view>
			<view class="months-content">
				<view class="months-content-item" v-for="(item,index) in months.items" :key="index">
					<go-details :list="months.items" :index="index">
						<image :src="item.thumb+item.rule.replace('$<Height>', 360)" mode="aspectFill" />
					</go-details>
				</view>
			</view>
		</view>
		<!-- 月份 结束 -->
		<!-- 热门 开始 -->
		<view class="hots-wrap">
			<view class="hots-title">
				<text> 热门 </text>
			</view>
			<view class="hots-content">
				<view class="hots-item" v-for="(item, index) in hots" :key="item.id">
					<go-details :list="hots" :index="index">
						<image :src="item.thumb" mode="widthFix" />
					</go-details>
				</view>
			</view>
		</view>
		<!-- 热门 结束 -->
	</scroll-view>
</template>

<script>
	import moment from 'moment'
	import goDetails from '@/components/goDetails'
	export default {
		components: {
			goDetails
		},
		data() {
			return {
				// 推荐列表
				recommends: [],
				// 月份
				months: {},
				// 热门
				hots: [],
				// 请求的参数
				params: {
					limit: 30,
					order: 'hot',
					skip: 0
				},
				// 是否还有下一页
				hasMore: true
			}
		},
		mounted() {
			// 修改页面的标题
			uni.setNavigationBarTitle({
				title: '推荐'
			})
			this.getList()
		},
		methods: {
			// 获取接口数据
			getList() {
				this.request({
					url: 'http://service.picasso.adesk.com/v3/homepage/vertical',
					data: this.params
				}).then(result => {
					// 判断还有没有下一页数据
					if (result.res.vertical.length === 0) {
						this.hasMore = false
						uni.showToast({
							title: '没有更多数据了',
							icon: 'none'
						})
						return
					}
					if (this.recommends.length === 0) {
						// 第一次发送的请求
						// 推荐模块
						this.recommends = result.res.homepage[1].items
						// 月份模块
						this.months = result.res.homepage[3]
						// 将时间戳 改为 18号/月 moment.js
						this.months.MM = moment(this.months.stime).format('MM')
						this.months.DD = moment(this.months.stime).format('DD')
					}
					// 获取热门数据列表
					// 数组拼接 es6
					this.hots = [...this.hots, ...result.res.vertical]
				})
			},
			// 滚动条触底事件
			handleToLower() {
				/*
					1. 修改参数 skip += limit
					2. 重新发送请求 getList()
					3. 请求回来成功 hots 数据的叠加
				*/
				if (this.hasMore) {
					this.params.skip += this.params.limit
					this.getList()
				} else {
					// 弹窗提示
					uni.showToast({
						title: '没有数据了',
						icon: "none"
					})
				}

			}
		}
	}
</script>

<style lang="scss" scoped>
	.recommend-view {
		// height = 屏幕的高度 - 头部标题的高度
		height: calc(100vh - 36px);
	}

	.recommend-wrap {
		// flex 布局
		display: flex;
		flex-wrap: wrap;

		.recommend-item {
			width: 50%;
			border: 5rpx solid #FFFFFF;
		}
	}

	.months-wrap {
		.months-title {
			display: flex;
			justify-content: space-between;
			padding: 20rpx;

			.months-title-info {
				color: $color;
				font-size: 30rpx;
				font-weight: 600;
				display: flex;

				.months-info {
					text {
						font-size: 36rpx;
					}
				}

				.months-text {
					font-size: 34rpx;
					color: #666;
					margin-left: 30rpx;
				}
			}

			.months-title-more {
				font-size: 24rpx;
				color: $color;
			}
		}

		.months-content {
			display: flex;
			flex-wrap: wrap;

			.months-content-item {
				width: 33.33%;
				border: 5rpx solid #FFFFFF;
			}
		}
	}

	.hots-wrap {
		.hots-title {
			padding: 20rpx;

			text {
				border-left: 20rpx solid $color;
				padding-left: 20rpx;
				font-size: 34rpx;
				font-weight: 600;
			}
		}

		.hots-content {
			display: flex;
			flex-wrap: wrap;

			.hots-item {
				width: 33.33%;
				border: 5rpx solid #FFFFFF;

				image {}
			}
		}
	}
</style>
