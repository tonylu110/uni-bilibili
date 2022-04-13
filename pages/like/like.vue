<template>
	<view class="content">
		<view class="notLogin">
			<text>你还未登录</text>
			<button type="primary" @click="goToLogin()">登录</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			goToLogin() {
				uni.navigateTo({
					url:'../login/login'
				})
			}
		}
	}
</script>

<style>
	@import url("./like.css");
</style>
