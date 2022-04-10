<template>
	<view class="content">
		<view class="search_main">
			<uni-search-bar 
				@confirm="search" 
				v-model="searchValue" 
				@cancel="cancel" 
				@clear="clear" 
				placeholder="请输入你要搜索的内容" 
				cancelButton="always">
			</uni-search-bar>
		</view>
		<view class="chress" v-if="chress">
			<ren-dropdown-filter :filterData="filterData" :defaultIndex="defaultIndex" @ed="ed" top="56px"></ren-dropdown-filter>
		</view>
		<view style="margin-top: 105px;"></view>
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
	import RenDropdownFilter from '@/components/ren-dropdown-filter/ren-dropdown-filter.vue'
	export default {
		components:{
			RenDropdownFilter
		},
		data() {
			return {
				searchValue: '',
				videoInfo: [],
				videoTitle: '',
				searchClick: false,
				page: 1,
				filterData:[
                    [
						{
							text: '默认排序',
							value: '0-0' ,
						}, 
						{
							text: '播放多', 
							value: '0-1' ,
						}, 
						{ 
							text: '新发布',
							value: '0-2' ,
						}, 
						{
							text: '弹幕多', 
							value: '0-3' ,
						},
					],
                    [
                    	{
                    		text: '全部时长',
                    		value: '1-0' ,
                    	}, 
                    	{
                    		text: '0-10 分钟', 
                    		value: '1-1' ,
                    	}, 
                    	{ 
                    		text: '10-30 分钟',
                    		value: '1-2' ,
                    	}, 
                    	{
                    		text: '30-60 分钟', 
                    		value: '1-3' ,
                    	},
						{
							text: '60 分钟 +', 
							value: '1-4' ,
						},
                    ]
                ],
                defaultIndex:[0,0],
				chress: false
			}
		},
		onReachBottom() {
			this.page = this.page + 1
			this.getList()
		},
		methods: {
			getList() {
				uni.request({
					url:'http://api.bilibili.com/x/web-interface/search/all/v2',
					data:{
						keyword: this.searchValue,
						page: this.page
					},success: (res) => {
						this.videoInfo = [...this.videoInfo, ...res.data.data.result[10].data]
					}
				})
			},
			search() {
				this.page = 1
				this.videoInfo = []
				this.getList()
				this.chress = true
			},
			clear() {
				this.searchValue = ''
			},
			cancel() {
				uni.navigateBack({
					
				})
			},
			ed(res) {
				console.log(res) 
			}
		}
	}
</script>

<style>
	@import url("./search.css");
</style>
