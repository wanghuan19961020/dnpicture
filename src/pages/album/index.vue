<template>
	<view>
		<!-- 专辑背景 开始 -->
		<view class="album-bg">
			<image :src="album.cover" mode="widthFix" />
			<view class="album-info">
				<view class="album-name">{{album.name}}</view>
				<view class="album-btn">关注专辑</view>
			</view>
		</view>
		<!-- 专辑背景 结束 -->
		<!-- 专辑作者 开始 -->
		<view class="album-author">
			<view class="album-author-info">
				<image :src="album.user.avatar" mode="widthFix"></image>
				<view class="album-author-name">{{album.user.name}}</view>
			</view>
			<view class="album-author-desc">
				<text>{{album.desc}}</text>
			</view>
		</view>
		<!-- 专辑作者 结束 -->
		<!-- 列表 开始 -->
		<view class="album-list">
			<view class="album-item" v-for="(item, index) in wallpaper" :key="item.id">
				<go-details :list="wallpaper" :index="index">
					<image :src="item.thumb+item.rule.replace('<$Height>', 360)" />
				</go-details>
			</view>
		</view>
		<!-- 列表 结束 -->
	</view>
</template>

<script>
	import goDetails from '@/components/goDetails'
	export default {
		components: {
			goDetails
		},
		data() {
			return {
				params: {
					limit: 30,
					order: 'new',
					skip: 0,
					// 1 返回值中 有 album 对象 表示第一次请求数据
					// 0 返回值中 只有 wallpaper 数组 不是第一次请求数据
					first: 1
				},
				id: -1,
				album: {},
				wallpaper: [],
				hasMore: true
			}
		},
		onLoad(option) {
			this.id = option.id
			// this.id = '5051b1b80a2ae0721400011e'
			this.getList()
		},
		// 页面触底，上拉加载下一页事件
		onReachBottom() {
			if (this.hasMore) {
				this.params.first = 0
				this.params.skip += this.params.limit
				this.getList()
			} else{
				uni.showToast({
					title: '没有更多数据了',
					icon: 'none'
				})
			}
		},
		methods: {
			getList() {
				this.request({
					url: `https://service.picasso.adesk.com/v1/wallpaper/album/${this.id}/wallpaper`,
					data: this.params
				}).then(result => {
					const {
						res: {
							album,
							wallpaper
						}
					} = result
					if (Object.keys(this.album).length === 0) {
						this.album = album
					}
					if (wallpaper.length === 0) {
						this.hasMore = false
						uni.showToast({
							title: '没有更多数据了',
							icon: 'none'
						})
						return
					}
					this.wallpaper = [...this.wallpaper, ...wallpaper]
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.album-bg {
		position: relative;

		image {}

		.album-info {
			position: absolute;
			width: 100%;
			left: 0;
			bottom: 0;
			display: flex;
			justify-content: space-between;
			align-items: center;
			height: 80rpx;
			color: #FFFFFF;
			padding: 0 15rpx;

			.album-name {
				font-size: 40rpx;
			}

			.album-btn {
				background-color: $color;
				width: 152rpx;
				height: 60rpx;
				display: flex;
				justify-content: center;
				align-items: center;
				border-radius: 10rpx;
			}
		}
	}
	
	.album-author {
		padding: 10rpx;
		.album-author-info{
			padding: 10rpx 0;
			display: flex;
			image{
				width: 50rpx;
			}
			.album-author-name{
				color: #000;
				margin-left: 15rpx;
			}
		}
		.album-author-desc{}
	}
	.album-list {
		display: flex;
		flex-wrap: wrap;
		.album-item {
			width: 33.33%;
			border: 3rpx solid #FFFFFF;
			image {
				height: 160rpx;
			}
		}
	}
</style>
