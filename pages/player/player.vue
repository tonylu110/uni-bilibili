<template>
	<view class="content">
		<view class="system_bar" :style="{height: systemBarHeight + 'px'}"></view>
		<video :src="src" autoplay="true" danmu-btn="true" enable-danmu="true" :title="title" id="video_bl" :style="{top: systemBarHeight + 'px'}"></video>
		<view class="danmu_main" :style="{marginTop: px2rpx42 + systemBarHeight + 'px'}">
			<input type="text" placeholder="发送友谊的弹幕吧" value="" />
			<div class="send">
				<uni-icons type="paperplane-filled" size="30" color="white"></uni-icons>
			</div>
		</view>
		<view class="vat" :style="{top: vatpx + systemBarHeight + 'px'}">
			<view class="vt" :style="{color: selColor}" @click="vat('v')">
				简介
			</view>
			<view class="vt" :style="{color: fselColor}" @click="vat('t')">
				评论
			</view>
		</view>
		<view class="va" v-show="vatShow" :style="{marginTop: px2rpx52 + 40 + systemBarHeight + 'px'}">
			<view class="owner">
				<image :src="ownerImg" mode=""></image>
				<view class="owner_text">
					<text class="ownm">{{ ownerName }}</text>
					<uni-collapse accordion :show-animation="true">
						<uni-collapse-item :title="title" :border="false" titleBorder="none">
							<view class="colcon">
								<text>{{ vInfo }}</text>
							</view>
						</uni-collapse-item>
					</uni-collapse>
				</view>
			</view>
			<view class="vd_info">
				<view class="video_play" style="margin-right: 10rpx;">
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
			<view v-for="(item,index) in moreVideo" :key="index" @click="pause()">
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
		<view class="ta" v-show="!vatShow" :style="{marginTop: px2rpx52 + 40 + systemBarHeight + 'px'}">
			<view class="taa" v-for="(item,index) in resq" :key='index'>
				<image :src="item.member.avatar" mode="" class="aimg"></image>
				<view class="imgAPa">
					<view class="paName">
						{{ item.member.uname }}
					</view>
					{{ item.content.message }}
				</view>
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
				videoUrl: '',
				moreVideo: [],
				bottom: false,
				videoImg: '',
				view: '',
				like: '',
				systemBarHeight: 0,
				px2rpx52: 0,
				px2rpx42: 0,
				vatpx: 0,
				selColor: '#20b0e3',
				fselColor: 'black',
				vatShow: true,
				oid: '',
				resq: [],
				pn: 1,
				scrollTop: 0,
				vtt: 0,
				ptt: 0
			}
		},
		onLoad(option) {
			this.px2rpx52 = this.px2rpx(522)
			this.px2rpx42 = this.px2rpx(422)
			this.vatpx = this.px2rpx(500)
			this.getSysteminfo()
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
			if (option.img == '' || this.oid == '') {
				uni.request({
					url: 'http://api.bilibili.com/x/web-interface/view',
					data: {
						bvid: option.id
					},
					success: (res) => {
						this.ownerImg = res.data.data.owner.face
						this.oid = res.data.data.aid
					}
				})
			}
		},
		onReady() {
			this.videoContext = uni.createVideoContext('video_bl')
		},
		onShow() {
			this.videoContext.play()
		},
		onReachBottom() {
			if (!this.vatShow) {
				this.pn = this.pn + 1
				this.getTa()
			}
		},
		onPageScroll(res) {
			this.scrollTop = res.scrollTop
		},
		methods: {
			onShare() {
				uni.shareWithSystem({
					summary: this.title,
					href: this.videoUrl
				})
			},
			pause() {
				this.videoContext.pause()
			},
			getSysteminfo() {
				uni.getSystemInfo({
					success: res => {
						this.systemBarHeight = res.statusBarHeight;
					}
				});
			},
			px2rpx(rpx) {
				return uni.upx2px(rpx)
			},
			vat(vot) {
				if (vot == 't') {
					this.vatShow = false
					this.selColor = 'black'
					this.fselColor = '#20b0e3'
					this.vtt = this.scrollTop;
					uni.pageScrollTo({
						scrollTop: this.ptt,
						duration: 0
					})
					if (!this.resq.length) {
						this.getTa()	
					}
				} else {
					this.vatShow = true
					this.selColor = '#20b0e3'
					this.fselColor = 'black'
					this.ptt = this.scrollTop
					uni.pageScrollTo({
						scrollTop: this.vtt,
						duration: 0
					})
				}
			},
			getTa() {
				uni.request({
					url:'https://api.bilibili.com/x/v2/reply',
					data:{
						jsonp: 'jsonp',
						pn: this.pn,
						type: '1',
						sort: '2',
						oid: this.oid
					},
					success: (res) => {
						this.resq = [...this.resq, ...res.data.data.replies]
					}
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