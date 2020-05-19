<template>
	<view>
		<!-- #ifdef MP-WEIXIN -->
		<view v-if="isCanUse">
			<view>
				<view class="header"><image src="/static/weixin.png"></image></view>

				<view class="content">
					<view>申请获取以下权限</view>
					<text>获得你的公开信息(昵称，头像、地区等)</text>
				</view>

				<button class="bottom" type="primary" open-type="getUserInfo" withCredentials="true" lang="zh_CN" @getuserinfo="wxGetUserInfo">授权登录</button>
			</view>
		</view>
		<!-- #endif -->
	</view>
</template>

<script>
import { mapState, mapActions, mapMutations } from 'vuex';

export default {
	data() {
		return {
			SessionKey: '',
			OpenId: '',
			nickName: null,
			avatarUrl: null,
			isCanUse: uni.getStorageSync('isCanUse') || true //默认为true
		};
	},
	computed: mapState(['forcedLogin', 'hasLogin', 'userName']),
	onLoad() {
		//默认加载
		// this.login();
	},
	methods: {
		...mapMutations(['login']),
		//第一授权获取用户信息===》按钮触发
		wxGetUserInfo() {
			let _this = this;
			uni.login({
				provider: 'weixin',
				success: res => {
					uni.getUserInfo({
						provider: 'weixin',
						success: function(infoRes) {
							console.log(infoRes);
							_this.nickName = infoRes.userInfo.nickName; //昵称
							_this.avatarUrl = infoRes.userInfo.avatarUrl; //头像
							console.log(_this.nickName + '\n' + _this.avatarUrl);
							/**
							 * 实际开发中，获取用户信息后，需要将信息上报至服务端。
							 * 服务端可以用 userInfo.openId 作为用户的唯一标识新增或绑定用户信息。
							 */
							_this.toMain(infoRes.userInfo.nickName);
							// try {
							// 	uni.setStorageSync('isCanUse', false); //记录是否第一次授权  false:表示不是第一次授权
							// 	_this.updateUserInfo();
							// } catch (e) {}
						},
						fail() {
							uni.showToast({
								icon: 'none',
								title: '登陆失败'
							});
						}
					});
				},
				fail: err => {
					console.error('授权登录失败：' + JSON.stringify(err));
				}
			});
		},
		toMain(userName) {
			this.login(userName);
			/**
			 * 强制登录时使用reLaunch方式跳转过来
			 * 返回首页也使用reLaunch方式
			 */
			if (this.forcedLogin) {
				uni.reLaunch({
					url: '../main/main'
				});
			} else {
				uni.navigateBack({
					delta: 2
				});
				// uni.reLaunch({
				// 	url: '../user/user',
				// });
			}
			wx.showToast({
				title: '登录成功',
				icon: 'success',
				duration: 1000
			});
		}

		//登录
		// login() {
		// 	let _this = this;
		// 	// 1.wx获取登录用户code
		// 	uni.login({
		// 		provider: 'weixin',
		// 		success: function(loginRes) {
		// 			let code = loginRes.code;
		// 			//2.将用户登录code存到缓存里
		// 			wx.setStorageSync('code', code);
		// 			console.log('!!!!!!!!!!!!!!!!!!!!');
		// 			console.log(_this.isCanUse);
		// 			if (!_this.isCanUse) {
		// 				uni.showToast({
		// 					title: '用户已授权',
		// 					icon: 'none',
		// 					duration: 1000
		// 				});
		// 				//非第一次授权获取用户信息
		// 				uni.getUserInfo({
		// 					provider: 'weixin',
		// 					success: function(infoRes) {
		// 						//获取用户信息后向调用信息更新方法
		// 						_this.nickName = infoRes.userInfo.nickName; //昵称
		// 						_this.avatarUrl = infoRes.userInfo.avatarUrl; //头像
		// 						_this.updateUserInfo(); //调用更新信息方法
		// 					}
		// 				});
		// 			}

		// 			//2.将用户登录code传递到后台置换用户SessionKey、OpenId等信息
		// 			// uni.request({
		// 			// 	url: '服务器地址',
		// 			// 	data: {
		// 			// 		code: code
		// 			// 	},
		// 			// 	method: 'GET',
		// 			// 	header: {
		// 			// 		'content-type': 'application/json'
		// 			// 	},
		// 			// 	success: res => {
		// 			// 		//openId、或SessionKdy存储//隐藏loading
		// 			// 		uni.hideLoading();
		// 			// 	}
		// 			// });
		// 		}
		// 	});
		// },

		//向后台更新信息  目前只能存入缓存，没有服务器
		// updateUserInfo() {
		// 	console.log('???????????????????????????');
		// 	let _this = this;

		// 	wx.setStorageSync('nickName', _this.nickName);
		// 	wx.setStorageSync('headUrl', _this.avatarUrl);
		// 	if (this.forcedLogin) {
		// 		uni.reLaunch({
		// 			url: '../main/main'
		// 		});
		// 	} else {
		// 		uni.reLaunch({
		// 			url: '../user/user'
		// 		});
		// 	}
		// 	// uni.navigateBack();
		// 	// uni.reLaunch({
		// 	// 	url: '../main/main'
		// 	// });

		// 	// uni.request({
		// 	// 	url: 'url', //服务器端地址
		// 	// 	data: {
		// 	// 		appKey: this.$store.state.appKey,
		// 	// 		customerId: _this.customerId,
		// 	// 		nickName: _this.nickName,
		// 	// 		headUrl: _this.avatarUrl
		// 	// 	},
		// 	// 	method: 'POST',
		// 	// 	header: {
		// 	// 		'content-type': 'application/json'
		// 	// 	},
		// 	// 	success: res => {
		// 	// 		if (res.data.state == 'success') {
		// 	// 			uni.reLaunch({
		// 	// 				//信息更新成功后跳转到小程序首页
		// 	// 				url: '/pages/index/index'
		// 	// 			});
		// 	// 		}
		// 	// 	}
		// 	// });
		// }
	}
};
</script>

<style>
.header {
	margin: 90rpx 0 90rpx 50rpx;
	border-bottom: 1px solid #ccc;
	text-align: center;
	width: 650rpx;
	height: 300rpx;
	line-height: 450rpx;
}

.header image {
	width: 200rpx;
	height: 200rpx;
}

.content {
	margin-left: 25rpx;
	margin-right: 25rpx;
	margin-bottom: 90rpx;
}

.content text {
	display: block;
	color: #9d9d9d;
	margin-top: 40rpx;
}

.bottom {
	border-radius: 80rpx;
	margin: 70rpx 50rpx;
	font-size: 35rpx;
	/* background-color: #0faeff; */
}
</style>
