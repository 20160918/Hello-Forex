<template>
	<view class="content">
		<view v-if="!hasLogin" style="text-align: center;height: 400upx;margin-top: 200upx;width: 100%;font-size: 20px;">
			<uni-card title="请先登录" thumbnail="https://img-cdn-qiniu.dcloud.net.cn/uniapp/images/cbd.jpg" is-shadow="true" mode="title">登录后才可进行操作！</uni-card>
		</view>
		<view v-if="hasLogin">
			<view v-if="mycourseid.indexOf(course_id) == -1" style="text-align: center;height: 400upx;margin-top: 200upx;width: 100%;font-size: 20px;">
				<button @tap="addstu_course()">立即拥有</button>
			</view>
			<view v-if="mycourseid.indexOf(course_id) != -1">
				<view style="text-align: center;height: 400upx;margin-top: 200upx;width: 100%;font-size: 20px;">
					<uni-card title="您已拥有该课程" thumbnail="https://img-cdn-qiniu.dcloud.net.cn/uniapp/images/cbd.jpg" is-shadow="true" mode="title">
						您可以选择其他课程！
					</uni-card>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
import { mapState, mapMutations } from 'vuex';
export default {
	data() {
		return {
			title: '',
			strings: '',
			mycourse: {},
			mycourseidlist:[],
			mycourseid: [],
			course_id: 0
		};
	},
	computed: mapState(['forcedLogin', 'hasLogin', 'userName']),
	onLoad: function(e) {
		this.course_id = parseInt(e.courseid) ,
			uni.request({
				url: 'http://127.0.0.1:5000/mycourse',
				method: 'POST',
				data: {
					stu_account: this.userName
				},
				header: {
					'content-type': 'application/json',
					chartset: 'utf-8'
				},
				success: res => {
					this.mycourse = res.data;
					for (var i = 0; i < this.mycourse.length; i++) {
						this.mycourseidlist.push(this.mycourse[i].course_id);
					}
					console.log(this.mycourseidlist);
					this.mycourseid = this.mycourseidlist;
					uni.hideLoading();
				}
			});
			
	},
	methods: {
		addstu_course() {
			uni.request({
				url: 'http://127.0.0.1:5000/addstu_course',
				method: 'POST',
				data: {
					stu_account: this.userName,
					course_id: this.course_id
				},
				header: {
					'content-type': 'application/json',
					chartset: 'utf-8'
				},
				success: res => {
					if (res.data == '1') {
						uni.redirectTo({
						    url: './main?id=true'
						});
						uni.navigateBack();
						uni.showToast({
							title: '添加成功！',
							icon: 'none',
							duration: 1000
						});
					}
				}
			});
		}
	}
};
</script>

<style>
.content {
	padding: 10upx 2%;
	width: 96%;
	flex-wrap: wrap;
	background-color: #ffffff;
}
.title {
	line-height: 2em;
	font-weight: 700;
	font-size: 38upx;
}
.art-content {
	line-height: 2em;
}
</style>
