<template>
	<view class="content">
		<view class="system_bar" :style="{height: systemBarHeight + 'px'}"></view>
		<view class="search_main" :style="{top: systemBarHeight + 'px'}">
			<uni-search-bar 
				@confirm="search" 
				v-model="searchValue" 
				@cancel="cancel" 
				@clear="clear" 
				placeholder="请输入你要搜索的内容" >
			</uni-search-bar>
		</view>
		<view class="drop_down" v-if="chress">
			<wyb-drop-down
			 ref="dropDown"
			 :options="options"
			 @change="onHeaderSelect"
			 @select="onItemSelect"
			 active-color="#20b0e3"
			 :bgColor="{header:'#fff',content: '#ffffffdd'}">
			</wyb-drop-down>
		</view>
		<view class="hotwords" :style="{marginTop: 70 + systemBarHeight + 'px'}" v-if="!chress">
			热门搜索：
			<view class="allHW">
				<view class="hotword" v-for="(item,index) in hotwords" :key="index" @click="hotSearch(item.keyword)">
					<image :src="item.icon" mode="" v-if="item.icon"></image>
					{{ item.keyword }}
				</view>
			</view>
			<view class="his_word" v-if="hisWordShow" :scroll-y="true">
				历史搜索：
				<view :scroll-y="true" class="allHW allHisW">
					<view class="hotword" v-for="(item,index) in keywords" :key="index" @click="hotSearch(item)">
						{{ item }}
					</view>
				</view>
				<view class="clear_btn" type="default" @click="clearHisword"><uni-icons type="trash" size="30" color="#aaa"></uni-icons>清除搜索历史</view>
			</view>
		</view>
		<view :style="{marginTop: 67 + dropHeight + systemBarHeight + 'px'}"></view>
		<view v-for="(item,index) in videoInfo" :key="index">
			<navigator class="video_list" hover-stay-time="0" :url="'../player/player?id=' + item.bvid + '&title=' + '&img=' + item.upic + '&name=' + item.author + '&desc=' + item.description + '&pic=' + item.pic + '&view=' + item.play + '&like=' + item.like">
				<image :src="item.pic" mode="" class="video_img"></image>
				<view class="video_info">
					<rich-text :nodes="item.title" class="video_title"></rich-text>
					<view class="up_main">
						<view class="up_name">
							<uni-icons type="person-filled" size="16" color="gray"></uni-icons>
							<text>{{ item.author }}</text>
						</view>
						<view class="video_in">
							<view class="video_play" style="margin-right: 10rpx;">
								<uni-icons type="videocam-filled" size="16" color="gray"></uni-icons>
								<text>{{ item.play }}</text>
							</view>
							<view class="video_like">
								<uni-icons type="hand-up-filled" size="16" color="gray"></uni-icons>
								<text>{{ item.like }}</text>
							</view>
						</view>
					</view>
				</view>
			</navigator>
		</view>
		<view style="padding: 20rpx 0rpx 20rpx 0rpx; display: flex; justify-content: center; align-items: center; color: gray;" v-if="searchClick">
			已经到底了哦~~
		</view>
	</view>
</template>

<script>
	import wybDropDown from '@/components/wyb-drop-down/wyb-drop-down.vue'
	export default {
		components: {
			wybDropDown
		},
		data() {
			return {
				searchValue: '',
				videoInfo: [],
				videoTitle: '',
				searchClick: false,
				page: 1,
				chress: false,
				order: 'default',
				duration: '0',
				systemBarHeight: 0,
				options: [{
					header: '默认排序',
					contents: ['默认排序', '播放多', '新发布', '弹幕多']
				}, {
					header: '全部时长',
					contents: ['全部时长', '0-10 分钟', '10-30 分钟', '30-60 分钟','60 分钟 +']
				}],
				hotwords: [],
				keywords: [],
				hisWordShow: false,
				dropHeight: 0
			}
		},
		onReachBottom() {
			this.page = this.page + 1
			this.getList(this.order,this.duration)
		},
		onLoad() {
			uni.request({
				url: 'http://s.search.bilibili.com/main/hotword',
				success: (res) => {
					this.hotwords = res.data.list
				}
			}),
			this.getSysteminfo()
			uni.getStorage({
				key: 'keywords',
				success: (res) => {
					this.keywords = res.data
				}
			});
			uni.getStorage({
				key: 'hisWordShow',
				success: (res) => {
					this.hisWordShow = res.data
				}
			})
			uni.$on('ddHeight',(data)=>{
				this.dropHeight = data
			})
		},
		methods: {
			getList(sorder,sduration) {
				uni.request({
					url:'http://api.bilibili.com/x/web-interface/search/type',
					data:{
						keyword: this.searchValue,
						page: this.page,
						order: sorder,
						duration: sduration,
						search_type: 'video'
					},success: (res) => {
						this.videoInfo = [...this.videoInfo, ...res.data.data.result]
					}
				})
			},
			getSysteminfo() {
				uni.getSystemInfo({
					success: res => {
						this.systemBarHeight = res.statusBarHeight;
					}
				});
			},
			search() {
				this.page = 1
				this.videoInfo = []
				this.getList(this.order,this.duration)
				this.chress = true
				var keyword = this.searchValue
				var keywords = this.keywords
				if (!keywords.includes(keyword)) {
					this.keywords = [keyword, ...keywords]
				} else {
					keywords = keywords.filter(item => item != keyword)
					this.keywords = [keyword, ...keywords]
				}
				uni.setStorage({
					key: 'keywords',
					data: this.keywords,
				})
				uni.setStorage({
					key: 'hisWordShow',
					data: true
				})
				this.hisWordShow = true
			},
			hotSearch(keyword) {
				this.searchValue = keyword
				this.page = 1
				this.videoInfo = []
				this.getList(this.order,this.duration)
				this.chress = true
				var keyword = this.searchValue
				var keywords = this.keywords
				if (!keywords.includes(keyword)) {
					this.keywords = [keyword, ...keywords]
				} else {
					keywords = keywords.filter(item => item != keyword)
					this.keywords = [keyword, ...keywords]
				}
				uni.setStorage({
					key: 'keywords',
					data: this.keywords,
				})
				uni.setStorage({
					key: 'hisWordShow',
					data: true
				})
				this.hisWordShow = true
			},
			clearHisword() {
				uni.removeStorage({
					key: 'keywords',
				})
				this.hisWordShow = false
				uni.setStorage({
					key: 'hisWordShow',
					data: false
				})
			},
			clear() {
				this.searchValue = ''
			},
			cancel() {
				this.searchValue = ''
				this.page = 1
				this.videoInfo = []
				this.chress = false
			},
			onHeaderSelect(res) {
				
			},
			onItemSelect(res) {
				var pdi = res.headerIndex.toString() + res.contentIndex.toString()
				if (pdi == '00') {
					this.options[0].header = '默认排序'
					this.order = 'default'
				} else if (pdi == '01') {
					this.options[0].header = '播放多'
					this.order = 'click'
				} else if (pdi == '02') {
					this.options[0].header = '新发布'
					this.order = 'pubdate'
				} else if (pdi == '03') {
					this.options[0].header = '弹幕多'
					this.order = 'damku'
				}
				if (pdi == '10') {
					this.options[1].header = '全部时长'
					this.duration = '0'
				} else if (pdi == '11') {
					this.options[1].header = '0-10 分钟'
					this.duration = '1'
				} else if (pdi == '12') {
					this.options[1].header = '10-30 分钟'
					this.duration = '2'
				} else if (pdi == '13') {
					this.options[1].header = '30-60 分钟'
					this.duration = '3'
				} else if (pdi == '14') {
					this.options[1].header = '60 分钟 +'
					this.duration = '4'
				}
				this.videoInfo = []
				this.getList(this.order,this.duration)
			}
		}
	}
</script>

<style>
	@import url("./search.css");
</style>
