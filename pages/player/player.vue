<template>
	<view class="content">
		<video :src="src" autoplay="true" danmu-btn="true" enable-danmu="true" :title="title"></video>
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
		<view class="vd_info">
			<view class="video_play" style="margin-right: 10rpx; margin-left: 20rpx;">
				<uni-icons type="videocam-filled" size="16" color="gray"></uni-icons>
				<text>{{ view }}</text>
			</view>
			<view class="video_like">
				<uni-icons type="hand-up-filled" size="16" color="gray"></uni-icons>
				<text>{{ like }}</text>
			</view>
		</view>
		<view class="fe_bar">
			<view class="fe">
				<uni-icons type="hand-up-filled" size="30" color="gray"></uni-icons>
			</view>
			<view class="fe">
				<uni-icons type="checkbox-filled" size="30" color="gray"></uni-icons>
			</view>
			<view class="fe">
				<uni-icons type="star-filled" size="30" color="gray"></uni-icons>
			</view>
			<view class="fe" @click="onShare()">
				<uni-icons type="redo-filled" size="30" color="gray"></uni-icons>
			</view>
		</view>
		<view v-for="(item,index) in moreVideo" :key="index">
			<navigator hover-stay-time="0" class="video_list" :url="'../player/player?id=' + item.bvid + '&title=' + item.title + '&img=' + item.owner.face + '&name=' + item.owner.name + '&desc=' + item.desc + '&pic=' + item.pic + '&view=' + item.stat.view + '&like=' + item.stat.like">
				<image :src="item.pic" mode="" class="video_img"></image>
				<view class="video_info">
					<text class="video_title">{{ item.title }}</text>
					<view class="up_main">
						<view class="up_name">
							<uni-icons type="person-filled" size="16" color="gray"></uni-icons>
							<text>{{ item.owner.name }}</text>
						</view>
						<view class="video_in">
							<view class="video_play" style="margin-right: 10rpx;">
								<uni-icons type="videocam-filled" size="16" color="gray"></uni-icons>
								<text>{{ item.stat.view }}</text>
							</view>
							<view class="video_like">
								<uni-icons type="hand-up-filled" size="16" color="gray"></uni-icons>
								<text>{{ item.stat.like }}</text>
							</view>
						</view>
					</view>
				</view>
			</navigator>
		</view>
		<view style="color: gray; padding: 20rpx 0px 20rpx 0rpx;" v-if="bottom">
			<text>不能往下划了哦 QAQ</text>
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
				videoUrl: '',
				moreVideo: [],
				bottom: false,
				videoImg: '',
				view: '',
				like: ''
			}
		},
		onLoad(option) {
			uni.request({
				url:'https://tenapi.cn/bilivideo/',
				data: {
					url: 'https://www.bilibili.com/video/' + option.id
				},
				success: (res) => {
					if (this.title == '') {
						this.title = res.data.title
						uni.setNavigationBarTitle({
							title: res.data.title
						})
					}
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
			this.videoImg = option.pic
			this.videoUrl = 'https://www.bilibili.com/video/' + option.id
			this.view = option.view
			this.like = option.like
			uni.request({
				url:'http://api.bilibili.com/x/web-interface/archive/related',
				data:{
					bvid: option.id
				},
				success: (res) => {
					this.moreVideo = res.data.data
					this.bottom = !this.bottom
				}
			})
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