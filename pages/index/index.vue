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
					<navigator :url="'../player/player?id=' + item.bvid + '&title=' + item.title + '&img=' + item.owner.face + '&name=' + item.owner.name + '&desc=' + item.desc">
						<image :src="item.pic" mode=""></image>
						<text>{{ item.title }}</text>
					</navigator>
				</div>
			</div>
			<div class="right">
				<div class="video" v-for="(item,index) in list_main" v-if="index % 2 !== 0">
					<navigator :url="'../player/player?id=' + item.bvid + '&title=' + item.title + '&img=' + item.owner.face + '&name=' + item.owner.name + '&desc=' + item.desc">
						<image :src="item.pic" mode=""></image>
						<text>{{ item.title }}</text>
					</navigator>
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
				page: 1,
				list_main: []
			}
		},
		onLoad() {
			if (uni.getSystemInfoSync().platform === 'android'){
				this.mobile = 'android'
			} else {
				this.mobile = 'other'
			}
			this.getList(false, 1)
			this.getList(false, false)
		},
		onPullDownRefresh() {
			setTimeout(() => {
				uni.stopPullDownRefresh();
				this.getList(false, 1)
			}, 1000);
		},
		onReachBottom () {
			var page = this.page = this.page + 1 
			uni.showToast({
				icon: 'loading',
				duration: 1100
			})
			setTimeout(() => {
				this.getList(true, page)
			}, 1000)
		},
		methods: {
			getList(bottom, page) {
				var list = []
				if (!bottom && page) {
					uni.request({
						url:'https://www.bilibili.com/index/ding.json?page=' + this.page,
						success: (res) => {
							this.list_main = res.data.douga
						}
					})
				} else if (bottom && page) {
					list = Object.values(this.list_main)
					uni.request({
						url:'https://www.bilibili.com/index/ding.json?page=' + page,
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
