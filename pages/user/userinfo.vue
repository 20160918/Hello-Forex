<template>
	<view style="text-align: center;height: 400upx;margin-top: 200upx;width: 100%;font-size: 20px;">
		<uni-card title="个人信息" thumbnail="https://img-cdn-qiniu.dcloud.net.cn/uniapp/images/cbd.jpg" is-shadow='true' mode='title'>
		    <view style="display: flex;flex-direction: column;text-align: left;margin-left: 40px;">
				<text>姓名：{{info[0].stu_name}}</text>
				<text>账户：{{info[0].stu_account}}</text>
				<text>性别：{{info[0].stu_sex}}</text>
				<text>年龄：{{info[0].stu_age}}</text>
				<text>电话：{{info[0].stu_phone}}</text>
			</view>
		</uni-card>
	</view>
</template>

<script>
import { mapState, mapMutations } from 'vuex';
import uniCard from '@/components/uni-card/uni-card.vue'

export default {
	data() {
		return {
			info:{}
		};
	},
	computed: mapState(['forcedLogin', 'hasLogin', 'userName']),
	onLoad:function(){
		uni.showLoading({
			title:"加载中...."
		})
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
				uni.hideLoading();
			}
		});
	},
	methods: {
		
	}
};

</script>

<style>
</style>
