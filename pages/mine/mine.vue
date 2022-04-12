<template>
	<view class="content">
		<image src="../../static/logo.png" mode="" class="user_img"></image>
		<view class="user_info" v-if="userLogin">
			<view class="">
				动态
			</view>
			<view class="">
				关注
			</view>
			<view class="">
				粉丝
			</view>
		</view>
		<view class="user_list" v-if="userLogin">
			<navigator hover-stay-time="0" url="" class="list">我的空间</navigator>
			<navigator hover-stay-time="0" url="" class="list">历史记录</navigator>
			<navigator hover-stay-time="0" url="" class="list">我的收藏</navigator>
			<navigator hover-stay-time="0" url="" class="list">设置</navigator>
		</view>
		<button type="primary">登录</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				userLogin: false
			}
		},
		methods: {
			
		}
	}
</script>

<style>
	@import url("./mine.css");
</style>
