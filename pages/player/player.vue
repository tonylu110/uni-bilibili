<template>
	<view class="content">
		<video :src="src" autoplay="true" danmu-btn="true" enable-danmu="true"></video>
		<view class="owner">
			<image :src="ownerImg" mode=""></image>
			<view class="owner_text">
				<text class="ownm">{{ ownerName }}</text>
				<uni-collapse accordion :show-animation="true">
					<uni-collapse-item title="简介" :border="false" titleBorder="none">
						<view class="colcon">
							<text>{{ vInfo }}</text>
						</view>
					</uni-collapse-item>
				</uni-collapse>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				src: '',
				title: '',
				ownerImg: '',
				ownerName: '',
				vInfo: ''
			}
		},
		onLoad(option) {
			uni.request({
				url:'https://tenapi.cn/bilivideo/',
				data: {
					url: 'https://www.bilibili.com/video/' + option.id
				},
				success: (res) => {
					this.src = res.data.url
				}
			})
			uni.setNavigationBarTitle({
				title:option.title
			})
			this.title = option.title
			this.ownerImg = option.img
			this.ownerName = option.name
			this.vInfo = option.desc
		},
		methods: {
			
		}
	}
</script>

<style>
	@import url("./player.css");
</style>

<style lang="scss">
	
</style>