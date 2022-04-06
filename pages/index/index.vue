<template>
	<view class="content">
		<div id="top_area" :class="mobile">
			<div class="search_area" @click="search">
				<uni-icons type="search" size="25"></uni-icons>
			</div>
		</div>
		<div id="list_top"></div>
		<div class="list" v-for="(item,index) in list_main">
			<div class="video">
				<image :src="item.pic" mode=""></image>
				<text>{{ item.title }}</text>
			</div>
			<div class="video">
				<image src="../../static/logo.png" mode=""></image>
				<text>hsuahkfjhd</text>
			</div>
		</div>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				mobile: '',
				img: '',
				titlt: '',
				list_main: []
			}
		},
		onLoad() {
			if (uni.getSystemInfoSync().platform === 'android'){
				this.mobile = 'android'
			} else {
				this.mobile = 'other'
			}
			console.log(uni.getSystemInfoSync().platform)
			uni.request({
				url:'http://bili.tnyl.xyz/api/recommend',
				success: (res) => {
					console.log(res.data)
					this.img = res.data.list[0].pic
					this.titlt = res.data.list[0].title
					this.list_main = res.data.list
				}
			})
		},
		methods: {
			search() {
				uni.navigateTo({
					url:'../search/search'
				})
			}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}
	
	#top_area{
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		background-color: #ffffff90;
		backdrop-filter: blur(20px);
		display: flex;
		justify-content: center;
		z-index: 2;
	}
	.other{
		margin-top: 44px;
	}
	.search_area{
		margin-top: 20rpx;
		margin-bottom: 20rpx;
		background-color: #00000010;
		width: 90%;
		height: 70rpx;
		display: flex;
		justify-content: center;
		align-items: center;
		border-radius: 10rpx;
	}
	
	#searchb{
		width: 730rpx;
	}
	
	#list_top{
		height: 120rpx;
	}
	.video{
		display: flex;
		flex-direction: column;
		margin: 0px 15rpx 20rpx 15rpx;
		border: 1px solid #00000010;
		justify-content: center;
		align-items: center;
		border-radius: 20rpx;
	}
	.video image{
		width: 320rpx;
		height: 200rpx;
		border-radius: 20rpx;
	}
	.video text{
		margin: 10rpx 0px 10rpx 0px;
		max-width: 300rpx;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap
	}
	.list{
		display: flex;
		flex-direction: row;
	}
	
	
</style>
