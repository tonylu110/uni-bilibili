<template>
	<view class="content">
		<div id="top_area" :class="mobile">
			<div class="search_area" @click="search">
				<uni-icons type="search" size="25"></uni-icons>
			</div>
		</div>
		<div id="list_top"></div>
		<view style="margin-top: 40rpx; font-size: 30rpx; font-weight: bold; color: gray;" v-if="no_net">没有网络的连接QAQ</view>
		<div class="list" v-if="!no_net">
			<div class="left">
				<div class="video" v-for="(item,index) in list_main" v-if="index % 2 === 0">
					<navigator hover-stay-time="0" :url="'../player/player?id=' + item.bvid + '&title=' + item.title + '&img=' + item.owner.face + '&name=' + item.owner.name + '&desc=' + item.desc + '&view=' + item.stat.view + '&like=' + item.stat.like">
						<image :src="item.pic" mode=""></image>
						<view class="own_i_p">
							<image :src="item.owner.face" mode=""></image>
							<view class="own_txt">
								<text class="title">{{ item.title }}\n<text class="owner_name">{{ item.owner.name }}</text></text>
							</view>
						</view>
					</navigator>
				</div>
			</div>
			<div class="right">
				<div class="video" v-for="(item,index) in list_main" v-if="index % 2 !== 0">
					<navigator hover-stay-time="0" :url="'../player/player?id=' + item.bvid + '&title=' + item.title + '&img=' + item.owner.face + '&name=' + item.owner.name + '&desc=' + item.desc + '&view=' + item.stat.view + '&like=' + item.stat.like">
						<image :src="item.pic" mode=""></image>
						<view class="own_i_p">
							<image :src="item.owner.face" mode=""></image>
							<view class="own_txt">
								<text class="title">{{ item.title }}\n<text class="owner_name">{{ item.owner.name }}</text></text>
							</view>
						</view>
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
				list_main: [],
				no_net: false
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
				this.list_main = []
				this.getList(false, 1)
			},300)
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
							uni.stopPullDownRefresh()
							this.no_net = false
						},
						fail: () => {
							this.no_net = true
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
