<template>
	<scroll-view class="album-scroll-view" scroll-y @scrolltolower="handleToLower">
		<!-- 
			swiper 
				1. 自动轮播属性 autoplay
				2. 指示器属性 indicator-dots
				3. 衔接轮播属性
				
				4. swiper
					默认的高度 150px
				5. image
					默认的宽度 320px => 基本样式中 重置了 100%
					默认的高度 240px
				6. 计算图片的宽度和高度的比例
				7. 把图片的比例也写到 swiper 标签样式
				8. swiper-item 宽高100%
		-->
		<!-- 轮播图 开始 -->
		<view class="album-swiper">
			<swiper autoplay indicator-dots circular>
				<swiper-item v-for="item in banner" :key="item.id">
					<image :src="item.thumb" mode="widthFix" />
				</swiper-item>
			</swiper>
		</view>
		<!-- 轮播图 结束 -->
		<!-- 列表 开始 -->
		<view class="album-list">
			<navigator class="album-item" v-for="item in album" :key="item.id" :url="`/pages/album/index?id=${item.id}`">
				<view class="album-img">
					<image :src="item.cover" mode="aspectFill" />
				</view>
				<view class="album-info">
					<view class="album-name">{{item.name}}</view>
					<view class="album-desc">{{item.desc}}</view>
					<view class="album-btn">
						<view class="album-attention">+关注</view>
					</view>
				</view>
			</navigator>
		</view>
		<!-- 列表 结束 -->
	</scroll-view>
</template>

<script>
	export default {
		data() {
			return {
				// 请求的参数
				params: {
					limit: 30,
					order: 'hot',
					skip: 0
				},
				// 轮播图数组
				banner: [],
				// 列表数组
				album: [],
				// 是否还有分页数据
				hasMore: true
			}
		},
		mounted() {
			// 修改页面的标题
			uni.setNavigationBarTitle({
				title: '专辑'
			})

			this.getList()
		},
		methods: {
			// 获取接口的数据
			getList() {
				this.request({
						url: 'https://service.picasso.adesk.com/v1/wallpaper/album',
						data: this.params
					})
					.then(result => {
						const {
							res: {
								banner,
								album
							}
						} = result
						if (this.banner.length === 0) {
							this.banner = banner
						}
						if (album.length === 0) {
							this.hasMore = false
							uni.showToast({
								title: '没有更多数据了',
								icon: 'none'
							})
							return
						}
						this.album = [...this.album, ...album]
					})
			},
			// 触底事件
			handleToLower() {
				if (this.hasMore) {
					this.params.skip += this.params.limit
					this.getList()
				} else {
					uni.showToast({
						title: '没有数据了',
						icon: 'none'
					})
				}
			}
		}
	}
</script>

<style lang="scss" scoped>
	.album-scroll-view {
		height: calc(100vh - 36px);
	}

	.album-swiper {
		swiper {
			// 750rpx 
			height: calc(750rpx / 2.3);

			image {
				height: 100%;
			}
		}
	}

	.album-list {
		padding: 10rpx;

		.album-item {
			padding: 10rpx 0;
			display: flex;
			border-bottom: 1rpx solid #ccc;

			.album-img {
				flex: 1;
				padding: 10rpx;

				image {
					width: 200rpx;
					height: 200rpx;
				}
			}

			.album-info {
				flex: 2;
				padding: 0 10rpx;
				overflow: hidden;

				.album-name {
					font-size: 30rpx;
					color: #000;
					padding: 10rpx 0;
				}

				.album-desc {
					padding: 10rpx 0;
					font-size: 24rpx;
					text-overflow: ellipsis;
					overflow: hidden;
					white-space: nowrap;
				}

				.album-btn {
					padding: 10rpx;
					display: flex;
					justify-content: flex-end;

					.album-attention {
						color: $color;
						border: 1rpx solid $color;
						padding: 10rpx;
					}
				}
			}
		}
	}
</style>
