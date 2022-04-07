<template>
	<view class="content">
		<view class="player">
			<yy-video-player 
			    :auto-play="false" 
			    :url="src"  
			    :poster="poster" 
			    :danmu-list="danmuList"  
			    :show-back-btn="false"
				style="width: 750rpx;"
			  ></yy-video-player>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				src: '',
				title: ''
			}
		},
		onLoad(option) {
			uni.request({
				url:'https://tenapi.cn/bilivideo/',
				data: {
					url: 'https://www.bilibili.com/video/' + option.id
				},
				success: (res) => {
					console.log(res.data)
					this.src = res.data.url
				}
			})
			uni.setNavigationBarTitle({
				title:option.title
			})
			this.title = option.title
		},
		methods: {
			
		}
	}
</script>

<style>
	@import url("./player.css");
</style>
