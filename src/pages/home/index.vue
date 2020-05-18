<template>
	<view class="home-tab">
		<view class="home-tab-title">
			<view class="title-inner">
				<uni-segmented-control :current="current" :values="items.map(v=>v.title)" @clickItem="onClickItem" style-type="text"
				 active-color="#d4237a" />
			</view>
			<view class="iconfont iconsearch"></view>
		</view>
		<view class="home-tab-content">
			<view v-if="current === 0">
				<home-recommend />
			</view>
			<view v-if="current === 1">
				<home-category />
			</view>
			<view v-if="current === 2">
				<home-new />
			</view>
			<view v-if="current === 3">
				<home-album />
			</view>
		</view>
	</view>
</template>

<script>
	import HomeAlbum from './home-album'
	import HomeCategory from './home-category'
	import HomeNew from './home-new'
	import HomeRecommend from './home-recommend'
	import {
		uniSegmentedControl
	} from '@dcloudio/uni-ui'
	// 推荐: http://service.picasso.adesk.com//v3/homepage/vertical
	// 专辑 https://service.picasso.adesk.com/v1/wallpaper/album
	// 分类: https://service.picasso.adesk.com/v1/vertical/category
	// 分类-最新-热门 https://service.picasso.adesk.com/v1/vertical/category/${id}/vertical
	// 专辑 https://service.picasso.adesk.com/v1/wallpaper/album
	// 专辑-详情 https://service.picasso.adesk.com/v1/wallpaper/album/${id}/wallpaper
	// 图片评论 https://service.picasso.adesk.com/v2/wallpaper/wallpaper/${id}/comment
	export default {
		components: {
			HomeAlbum,
			HomeCategory,
			HomeNew,
			HomeRecommend,
			uniSegmentedControl
		},
		data() {
			return {
				items: [{
						title: '推荐'
					},
					{
						title: '分类'
					},
					{
						title: '最新'
					},
					{
						title: '专辑'
					}
				],
				current: 1
			}
		},
		methods: {
			onClickItem(e) {
				if (this.current !== e.currentIndex) {
					this.current = e.currentIndex;
				}
			}
		}
		// onLoad() {
		// 	this.request({
		// 		url: 'http://service.picasso.adesk.com/v3/homepage/vertical'
		// 	}).then(res => {
		// 		console.log(res);
		// 	})
		// }
	}
</script>

<style lang="scss">
	.home-tab {
		.home-tab-title {
			position: relative;
			.title-inner {
				width: 60%;
				margin: 0 auto;
			}
			.iconsearch {
				position: absolute;
				top: 50%;
				transform: translateY(-50%);
				right: 5%;
			}
		}
		.home-tab-content {
			
		}
		
	}
	
</style>
