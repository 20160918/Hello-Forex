<template>
	<view class="center content">
		<view class="center_top"><view class="mask"></view></view>
		<view class="center_box_bg">
			<view class="profily">
				<view class="base">
					<view class="profily_header"></view>
					<text v-if="!hasLogin">昵称</text>
					<text v-if="hasLogin">{{userName}}</text>
					<!-- <text>昵称</text> -->
					<image src="../../static/fumou-center-template/setting.png" mode=""></image>
				</view>
				<!-- <view class="order_status">
					<view class="status" v-for="(item,key) in status" :key=key>
						<image class="icon" :src="item.url" mode="aspectFill"></image>
						<text>{{ item.name }}</text>
					</view>
				</view> -->
			</view>
			<view class="baiban"></view>
			<view class="center_menu">
				<view class="menu_item" v-if="!hasLogin">
					<image src='../../static/fumou-center-template/9.png' mode="aspectFill"></image>
					<text>个人资料</text>
				</view>
				<view class="menu_item" @tap="openinfo" v-if="hasLogin" >
					<image src='../../static/fumou-center-template/9.png' mode="aspectFill"></image>
					<text>个人资料</text>
				</view>
				<view class="menu_item">
					<image src='../../static/fumou-center-template/5.png' mode="aspectFill"></image>
					<text>我的课程</text>
					<text v-if="hasLogin" style="text-align: right;padding-left: 160px;">{{info[0].stu_courseNum}}门</text>
				</view>
				<view class="menu_item">
					<image src='../../static/fumou-center-template/8.png' mode="aspectFill"></image>
					<text>消息中心</text>
					<text v-if="hasLogin" style="text-align: right;padding-left: 160px;">暂无</text>
				</view>
				<view class="menu_item">
					<image src='../../static/fumou-center-template/10.png' mode="aspectFill"></image>
					<text>关于我们</text>
					<text v-if="hasLogin" style="text-align: right;padding-left: 160px;">暂无</text>
				</view>
				<!-- <view class="menu_item" v-for="(item,key) in menus" :key=key>
					<image :src="item.icon" mode="aspectFill"></image>
					<text>{{ item.name }}<text></text></text>
				</view> -->
			</view>
		</view>

		<view class="btn-row" style=" display: flex;margin-top: 30px;">
			<button v-if="!hasLogin" type="primary" class="primary" @tap="bindLogin">登录</button>
			<button v-if="hasLogin" type="default" @tap="bindLogout">退出登录</button>
		</view>
	</view>
</template>

<script>
import { mapState, mapMutations } from 'vuex';

export default {
	data() {
		return {
			status: [
				// {
				// 	key: 1,
				// 	name: '完成课程',
				// 	url: '../../static/fumou-center-template/one.png'
				// },
				// {
				// 	key: 2,
				// 	name: '我的积分',
				// 	url: '../../static/fumou-center-template/2.png'
				// },
				// {
				// 	key: 3,
				// 	name: '待评价',
				// 	url: '../../static/fumou-center-template/3.png'
				// },
				// {
				// 	key: 4,
				// 	name: '全部订单',
				// 	url: '../../static/fumou-center-template/4.png'
				// }
			],
			menus: [
				{
					name: '个人资料',
					icon: '../../static/fumou-center-template/9.png',
					key: 1
				},
				{
					name: '我的课程',
					icon: '../../static/fumou-center-template/5.png',
					key: 2
				},
				// {
				// 	name: '我的题库',
				// 	icon: '../../static/fumou-center-template/6.png',
				// 	key: 2
				// },
				// {
				// 	name: '学习记录',
				// 	icon: '../../static/fumou-center-template/7.png',
				// 	key: 3
				// },
				{
					name: '消息中心',
					icon: '../../static/fumou-center-template/8.png',
					key: 3
				},
				{
					name: '关于我们',
					icon: '../../static/fumou-center-template/10.png',
					key: 4
				}
			],
			info:{}
		};
	},
	computed: mapState(['forcedLogin', 'hasLogin', 'userName']),
	onLoad:function(){
		if(this.hasLogin){
			uni.request({
				url: 'http://127.0.0.1:5000/secondrecommend1',
				method: 'POST',
				data: {
					stu_account: this.userName
				},
				header: {
					'content-type': 'application/json',
					chartset: 'utf-8'
				},
				success: res => {
					this.info = res.data;
					console.log(this.info);
					console.log(this.info[0].stu_courseNum);
				}
			});
		}
	},
	 onPullDownRefresh() {
	        console.log('refresh');
			uni.request({
				url: 'http://127.0.0.1:5000/secondrecommend1',
				method: 'POST',
				data: {
					stu_account: this.userName
				},
				header: {
					'content-type': 'application/json',
					chartset: 'utf-8'
				},
				success: res => {
					this.info = res.data;
					console.log(this.info);
					console.log(this.info[0].stu_courseNum);
				}
			});
	        setTimeout(function () {
	            uni.stopPullDownRefresh();
	        }, 1000);
	    },
	methods: {
		...mapMutations(['logout']),
		bindLogin() {
			uni.navigateTo({
				url: '../login/login'
			});
		},
		bindLogout() {
			this.logout();
			/**
			 * 如果需要强制登录跳转回登录页面
			 */
			if (this.forcedLogin) {
				uni.reLaunch({
					url: '../login/login'
				});
			}
		},
		openinfo() {
			uni.navigateTo({
				url: './userinfo',
			});
		},
	}
};
</script>

<style lang="scss">
page {
	height: 100%;
}

.profily,
.profily_header {
	border-radius: 8px;
}

.center {
	height: 100%;

	&_top {
		height: 18%;
		background: url('../../static/fumou-center-template/header.jpg') no-repeat 0% 50%;
		background-size: cover;

		// background: #E6E6E6;
		.mask {
			background: rgba(0, 0, 0, 0.4);
			height: 100%;
		}
	}

	&_box_bg {
		background: #f9f9f9;
		position: relative;

		.profily {
			position: absolute;
			width: 90%;
			// border:1px solid #F7F7F7;
			margin: 0 auto;
			top: -100upx;
			left: 5%;
			background: #fefefe;
			padding: 35upx;
			box-sizing: border-box;
			box-shadow: 0px 2px 5px #ededed;
		}
	}
}

.base {
	display: flex;
	align-items: center;
	border-bottom: 2px solid #f6f6f6;
	padding-bottom: 35upx;
	position: relative;
	.profily_header {
		height: 120upx;
		width: 120upx;
		background-image: url('../../static/fumou-center-template/header.jpg');
		background-size: 100%;
	}

	text {
		margin-left: 20px;
		font-size: 30upx;
	}

	image {
		position: absolute;
		height: 40upx;
		width: 40upx;
		right: 0px;
		top: 0px;
	}
}

.order_status {
	display: flex;
	justify-content: space-between;
	margin-top: 35upx;

	.status {
		width: 140upx;
		font-size: 24upx;
		text-align: center;
		letter-spacing: 0.5px;
		display: flex;
		flex-direction: column;
		.icon {
			width: 50upx;
			height: 50upx;
			margin: 0 auto;
			margin-bottom: 5px;
		}
	}
}

.baiban {
	background: #fefefe;
	// height: 260upx;
	height: 160upx;
	border-bottom:1px solid #efefef;
}

.center_menu {
	width: 100%;
	display: inline-block;

	.menu_item {
		font-size: 28upx;
		letter-spacing: 1px;
		padding: 14px 5%;
		background: #fefefe;
		overflow: hidden;
		box-sizing: border-box;
		display: flex;
		align-items: center;
		position: relative;
		border-bottom: 1px solid #efefef;

		&:hover {
			background: #f6f6f6 !important;
		}

		&::after {
			content: '';
			width: 30upx;
			height: 30upx;
			position: absolute;
			right: 5%;
			background: url('../../static/fumou-center-template/right.png') no-repeat;
			background-size: 100%;
		}

		text:nth-of-type(1) {
			margin-left: 10px;
		}

		image {
			width: 40upx;
			height: 40upx;
		}

		// &:nth-of-type(4) {
		// 	margin-top: 10px;
		// }
	}
}
</style>
