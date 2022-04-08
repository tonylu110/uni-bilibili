<template>
	<view class="content">
		<video :src="src" autoplay="true" danmu-btn="true" enable-danmu="true"></video>
		<view class="danmu_main">
			<input type="text" placeholder="发送友谊的弹幕吧" value="" />
			<div class="send">
				<uni-icons type="paperplane-filled" size="30" color="white"></uni-icons>
			</div>
		</view>
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
		<view class="fe_bar">
			<view class="fe">
				<uni-icons type="hand-up-filled" size="40" color="gray"></uni-icons>
			</view>
			<view class="fe">
				<uni-icons type="checkbox-filled" size="40" color="gray"></uni-icons>
			</view>
			<view class="fe" @click="onShare()">
				<uni-icons type="redo-filled" size="40" color="gray"></uni-icons>
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
				vInfo: '',
				videoUrl: ''
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
			this.videoUrl = 'https://www.bilibili.com/video/' + option.id
		},
		methods: {
			onShare() {
				uni.shareWithSystem({
					summary: this.title,
					href: this.videoUrl
				})
			}
		}
	}
</script>

<style>
	@import url("./player.css");
</style>

<style lang="scss">
	.colcon{
		padding-bottom: 15px;
	}
</style>