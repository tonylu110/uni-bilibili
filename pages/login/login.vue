<template>
	<view class="content">
		<image src="../../static/0-n.png" mode=""></image>
		<view class="user_login_list">
			<input type="text" value="" placeholder="请输入电话号码" />
			<view class="mm_value">
				<input type="text" value="" placeholder="请输入验证码" />
				<button type="default" class="mm_btn">获取验证码</button>
			</view>
			<button type="primary" class="login_btn">登录</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		onLoad() {
			uni.request({
				url:'https://passport.bilibili.com/x/passport-login/captcha?source=main_web',
				success: (res) => {
					console.log(res.data)
				}
			})
		},
		methods: {
			
		}
	}
</script>

<style>
	@import url("./login.css");
</style>
