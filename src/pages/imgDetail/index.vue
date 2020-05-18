<template>
	<view>
		<!-- 用户信息 开始 -->
		<view class="user-info">
			<view class="user-icon">
				<image :src="imgDetail.user.avatar" mode="widthFix" />
			</view>
			<view class="user-desc">
				<view class="user-name">{{imgDetail.user.name}}</view>
				<view class="user-time">{{imgDetail.cnTime}}</view>
			</view>
		</view>
		<!-- 用户信息 结束 -->

		<!-- 高清大图 开始 -->
		<view class="high-img">
			<!-- <image :src="imgDetail.thumb" mode="widthFix" /> -->
			<swiper-action @swiperAction="handleSwiperAction">
				<image :src="imgDetail.thumb" mode="widthFix" />
			</swiper-action>
		</view>
		<!-- 高清大图 结束 -->

		<!-- 点赞 开始 -->
		<view class="user-rank">
			<view class="rank">
				<text class="iconfont icondianzan">{{imgDetail.rank}}</text>
			</view>
			<view class="user-collect">
				<text class="iconfont iconshoucang">收藏</text>
			</view>
		</view>
		<!-- 点赞 结束 -->

		<!-- 专辑 开始 -->
		<view class="album-wrap" v-if="album.length">
			<!-- 标题 -->
			<view class="album-title">相关</view>
			<!-- 内容 -->
			<view class="album-list">
				<view class="album-item" v-for="item in album" :key="item.id">
					<view class="album-cover">
						<image :src="item.cover" mode="aspectFill" />
					</view>
					<view class="album-info">
						<view class="album-info-text">专辑</view>
						<view class="album-name">{{item.name}}</view>
						<text class="iconfont iconiconfontjiantou4"></text>
					</view>
				</view>
			</view>
		</view>
		<!-- 专辑 结束 -->

		<!-- 最热评论 comment hot -->
		<view class="comment hot" v-if="hot.length">
			<view class="comment-title">
				<text class="iconfont iconhot1"></text>
				<text class="comment-text">最热评论</text>
			</view>
			<view class="comment-list">
				<view class="comment-item" v-for="item in hot" :key="item.id">
					<!-- 用户信息 -->
					<view class="comment-user">
						<!-- 用户头像 -->
						<view class="user-icon">
							<image :src="item.user.avatar" mode="widthFix" />
						</view>
						<!-- 用户名称 -->
						<view class="user-name">
							<view class="user-nickname">{{item.user.name}}</view>
							<view class="user-time">{{item.cnTime}}</view>
						</view>
						<!-- 用户徽章 -->
						<view class="user-badge">
							<image v-for="item2 in item.user.title" :key="item2.icon" :src="item2.icon" />
						</view>
					</view>
					<!-- 评论数据 -->
					<view class="comment-desc">
						<view class="comment-content">{{item.content}}</view>
						<view class="comment-like">
							<text class="iconfont icondianzan">{{item.size}}</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 最热评论 comment hot -->
		<!-- 最新评论 comment hot -->
		<view class="comment new" v-if="comment.length">
			<view class="comment-title">
				<text class="iconfont iconpinglun"></text>
				<text class="comment-text">最新评论</text>
			</view>
			<view class="comment-list">
				<view class="comment-item" v-for="item in comment" :key="item.id">
					<!-- 用户信息 -->
					<view class="comment-user">
						<!-- 用户头像 -->
						<view class="user-icon">
							<image :src="item.user.avatar" mode="widthFix" />
						</view>
						<!-- 用户名称 -->
						<view class="user-name">
							<view class="user-nickname">{{item.user.name}}</view>
							<view class="user-time">{{item.cnTime}}</view>
						</view>
						<!-- 用户徽章 -->
						<view class="user-badge">
							<image v-for="item2 in item.user.title" :key="item2.icon" :src="item2.icon" />
						</view>
					</view>
					<!-- 评论数据 -->
					<view class="comment-desc">
						<view class="comment-content">{{item.content}}</view>
						<view class="comment-like">
							<text class="iconfont icondianzan">{{item.size}}</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 最新评论 comment hot -->
		<!-- 下载 开始 -->
		<view class="download">
			<view class="download-btn" @click="handleDownload">下载图片</view>
		</view>
		<!-- 下载 结束 -->
	</view>
</template>

<script>
	import moment from 'moment'
	import swiperAction from "@/components/swiperAction"
	// 设置语言为中文
	moment.locale('zh-cn')
	export default {
		components: {
			swiperAction
		},
		data() {
			return {
				// 图片信息对象 包含着用户头像 ...
				imgDetail: {},
				// 专辑数据 数组
				album: [],
				// 最热评论
				hot: [],
				// 最新评论
				comment: [],
				imgIndex: 0
			}
		},
		onLoad() {
			// console.log(getApp().globalData);
			const {
				imgIndex
			} = getApp().globalData
			this.imgIndex = imgIndex
			this.getList()
		},
		methods: {
			getList() {
				const {
					imgList
				} = getApp().globalData
				this.imgDetail = imgList[this.imgIndex]
				// this.imgDetail.newThumb = this.imgDetail.thumb + this.imgDetail.rule.replace('$<Height>', 360)
				this.imgDetail.cnTime = moment(this.imgDetail.atime * 1000).fromNow()
				// 获取图片详情的 id
				this.getComments(this.imgDetail.id)
			},
			// 点击下载图片 async await
			async handleDownload() {
				await uni.showLoading({
					title: '下载中'
				})
				// 1. 将远程文件下载到小程序的内存中 tempFilePath
				const result1 = await uni.downloadFile({
					url: this.imgDetail.img
				})
				const {
					tempFilePath
				} = result1[1]
				// 2. 将小程序中的临时文件下载到本地
				const result2 = await uni.saveImageToPhotosAlbum({
					filePath: tempFilePath
				})
				// 3. 提示用户下载成功
				// console.log('下载成功');
				uni.hideLoading()
				await uni.showToast({
					title: '下载成功',
					icon: 'none'
				})
			},
			getComments(id) {
				this.request({
					url: `https://service.picasso.adesk.com/v2/wallpaper/wallpaper/${id}/comment`
				}).then(result => {
					// console.log(result);
					this.album = result.res.album
					// 给 hot 数组中的对象添加一个时间属性 xxx月前
					result.res.hot.forEach(v => v.cnTime = moment(v.atime * 1000).fromNow())
					result.res.comment.forEach(v => v.cnTime = moment(v.atime * 1000).fromNow())
					this.hot = result.res.hot
					this.comment = result.res.comment
				})
			},
			handleSwiperAction(e) {
				const {
					imgList
				} = getApp().globalData;
				/* 
				  1 用户左滑 加载上一页 
				  2 用户右滑 加载下一页
				  3 将以前发送请求的代码封装成函数 getList()
				  4 减少没有必要的赋值
				  5 调用函数前判断用户的手势操作
				*/
				// imgList.length - 1是因为imgIndex是从0开始计算的
				if (e.direction === "left" && imgList.length - 1 > this.imgIndex) {
					this.imgIndex++;
					this.getList();
				} else if (e.direction === "right" && this.imgIndex > 0) {
					this.imgIndex--;
					this.getList();
				} else {
					uni.showToast({
						title: '没有数据了',
						icon: 'none'
					})
					return;
				}
			}
		}
	}
</script>

<style lang="scss" scoped>
	.user-info {
		display: flex;
		padding: 20rpx;

		.user-icon {
			padding: 0 20rpx;

			image {
				width: 88rpx;
				border-radius: 50%;
			}
		}

		.user-desc {
			.user-name {
				font-weight: 600;
				color: #000;
			}

			.user-time {
				color: #ccc;
				font-size: 24rpx;
				padding: 10rpx 0;
			}
		}
	}

	.high-img {
		image {
			border-bottom: 1rpx solid #eee;
		}
	}

	.user-rank {
		display: flex;
		padding: 20rpx 0;
		border-bottom: 5rpx solid #eee;

		.rank {
			display: flex;
			justify-content: center;
			align-items: center;
			flex: 1;

			.iconfont {}
		}

		.user-collect {
			display: flex;
			justify-content: center;
			align-items: center;
			flex: 1;

			.iconfont {}
		}
	}

	.album-wrap {
		padding: 20rpx;

		.album-title {
			padding: 10rpx 0;
		}

		.album-list {
			.album-item {
				display: flex;
				padding: 10rpx 0;
				border-bottom: 10rpx solid #edd;

				.album-cover {
					flex: 1;

					image {
						width: 180rpx;
						height: 180rpx;
					}
				}

				.album-info {
					flex: 3;
					padding-left: 20rpx;
					position: relative;

					.album-info-text {
						width: 100rpx;
						height: 50rpx;
						background-color: $color;
						color: #fff;
						display: flex;
						justify-content: center;
						align-items: center;
					}

					.album-name {
						padding: 10rpx 0;
						color: #888;
					}

					.iconfont {
						font-size: 40rpx;
						position: absolute;
						top: 50%;
						transform: translateY(-50%);
						right: 10%;
						color: #000;
					}
				}
			}
		}
	}

	.comment {
		.comment-title {
			padding: 15rpx;

			.iconfont {
				color: red;
				font-size: 40rpx;
			}

			.comment-text {
				font-weight: 600;
				font-size: 28rpx;
				color: #666;
				margin-left: 10rpx;
			}
		}

		.comment-list {
			.comment-item {
				// 用户信息
				border-bottom: 15rpx solid #eee;

				.comment-user {
					display: flex;
					padding: 20rpx 0;

					.user-icon {
						width: 15%;
						display: flex;
						justify-content: center;
						align-items: center;

						image {
							width: 90%;
						}
					}

					.user-name {
						flex: 1;

						.user-nickname {
							color: #777;
						}

						.user-time {
							color: #ccc;
							font-size: 24rpx;
							padding: 5rpx;
						}
					}

					.user-badge {
						image {
							width: 40rpx;
							height: 40rpx;
							display: inline-block;
						}
					}
				}

				// 评论数据
				.comment-desc {
					display: flex;
					padding: 30rpx 0;

					.comment-content {
						flex: 1;
						padding-left: 15%;
						color: #000;
					}

					.comment-like {
						text-align: right;

						.iconfont {}
					}
				}

			}
		}
	}

	.new {
		.iconpinglun {
			color: aqua !important;
		}
	}

	.download {
		height: 120rpx;
		display: flex;
		align-items: center;
		justify-content: center;

		.download-btn {
			width: 90%;
			height: 80%;
			background-color: $color;
			color: #fff;
			font-size: 50rpx;
			font-weight: 600;
			display: flex;
			justify-content: center;
			align-items: center;
		}
	}
</style>
