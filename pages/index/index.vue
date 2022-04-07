<template>
	<view class="content">
		<div id="top_area" :class="mobile">
			<div class="search_area" @click="search">
				<uni-icons type="search" size="25"></uni-icons>
			</div>
		</div>
		<div id="list_top"></div>
		<div class="list">
			<div class="left">
				<div class="video" v-for="(item,index) in list_main" v-if="index % 2 === 0">
					<image :src="item.pic" mode=""></image>
					<text>{{ item.title }}</text>
				</div>
			</div>
			<div class="right">
				<div class="video" v-for="(item,index) in list_main" v-if="index % 2 !== 0">
					<image :src="item.pic" mode=""></image>
					<text>{{ item.title }}</text>
				</div>
			</div>
		</div>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				mobile: '',
				list_main: []
			}
		},
		onLoad() {
			if (uni.getSystemInfoSync().platform === 'android'){
				this.mobile = 'android'
			} else {
				this.mobile = 'other'
			}
			this.getList()
		},
		onPullDownRefresh() {
			setTimeout(() => {
				uni.stopPullDownRefresh();
				this.getList()
			}, 1000);
		},
		onReachBottom () {
			setTimeout(() => {
				this.getList(true)
			},1500)
		},
		methods: {
			getList(bottom) {
				if (!bottom) {
					uni.request({
						url:'https://bili.tnyl.xyz/index/ding.json',
						success: (res) => {
							this.list_main = res.data.douga
						}
					})
				} else {
					var list = []
					list = Object.values(this.list_main)
					uni.request({
						url:'https://bili.tnyl.xyz/index/ding.json',
						success: (res) => {
							let more_list = Object.values(res.data.douga)
							this.list_main = [...list, ...more_list]
						}
					})
				}
			},
			search() {
				uni.navigateTo({
					url:'../search/search'
				})
			}
		}
	}
</script>

<style>
	@import url("./index.css");
</style>
