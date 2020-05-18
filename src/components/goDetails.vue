<template>
	<view @click="handleClick">
		<slot />
	</view>
</template>

<script>
	export default {
		props: {
			list: Array,
			index: Number
		},
		mounted() {
			// console.log(this.list, this.index);
		},
		methods: {
			handleClick() {
				// 1. 将数据缓存下来
				getApp().globalData.imgList = this.list
				getApp().globalData.imgIndex = this.index
				// 2. 再实现点击跳转页面
				uni.navigateTo({
					url: '/pages/imgDetail/index'
				})
			}
		}
	}
</script>

<style lang="scss">

</style>
