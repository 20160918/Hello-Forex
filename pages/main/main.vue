<template>
	<view class="page_edu">
		<view class="page_edu_header">
			<view class="haibao">
				<swiper :indicator-dots="indicatorDots" :autoplay="autoplay" :interval="interval" :duration="duration">
					<block v-for="(item, index) in imgUrls" :key="index">
						<swiper-item><image :src="item" class="slide-image"></image></swiper-item>
					</block>
				</swiper>
			</view>
		</view>
		<view class="page_content">
			<view class="container">
				<view style="padding: 6px;background-color: #FFFFFF;">
					<image src="/static/img/课程%20学业.png" style="width:30px;height:30px;margin-right:10px;"></image>
					<text style="font-size: 24px;font-weight: 500;">全部课程</text>
				</view>
				
				<block v-for="(item, index) in course" :key="index">
					<view class="hr"></view>
					<view :data-courseid="item.course_id" @tap="openinfo">
						<uni-list>
							<uni-list-item :note="item.course_studyNum+'人正在学习'" :showArrow="true">
								<view style="display: flex;">
									<text style="padding: 20px 3px;width: 70%;">{{ item.course_name }}</text>
									<image style="width: 80px;height: 80px;" src="/static/img/home-icon.png" mode="widthFix"></image>
								</view>
								
							</uni-list-item>
						</uni-list>
					</view>
				</block>
			</view>
		</view>
	</view>
</template>

<script>
import { mapState, mapActions } from 'vuex';
import uniList from '@dcloudio/uni-ui/lib/uni-list/uni-list.vue';
import uniListItem from '@dcloudio/uni-ui/lib/uni-list-item/uni-list-item.vue';

export default {
	data() {
		return {
			indicatorDots: false,
			autoplay: true,
			interval: 5000,
			duration: 1000,
			imgUrls: ['/static/img/haibao/haibao1.jpg', '/static/img/haibao/haibao2.jpg', '/static/img/haibao/haibao3.jpg', '/static/img/haibao/haibao4.jpg', '/static/img/haibao/haibao5.jpg', '/static/img/haibao/haibao6.jpg'],
			course: {},
			mycourse:{},
			mycourseid:[]
		};
	},
	computed: mapState(['forcedLogin', 'hasLogin', 'userName']),
	components: {
		uniList,
		uniListItem
	},
	onLoad() {
		if (!this.hasLogin) {
			uni.showModal({
				title: '未登录',
				content: '您未登录，需要登录后才能继续',
				/**
				 * 如果需要强制登录，不显示取消按钮
				 */
				showCancel: !this.forcedLogin,
				success: res => {
					if (res.confirm) {
						/**
						 * 如果需要强制登录，使用reLaunch方式
						 */
						if (this.forcedLogin) {
							uni.reLaunch({
								url: '../login/login'
							});
						} else {
							uni.navigateTo({
								url: '../login/login'
							});
						}
					}
				}
			});
		};
		uni.showLoading({
			title:"加载中...."
		})
		uni.request({
			url: 'http://127.0.0.1:5000/home',
			method: 'GET',
			data: {},
			header: {
				'custom-header': 'hello' //自定义请求头信息
			},
			success: res => {
				this.course = res.data;
				uni.hideLoading();
			}
		});

	},
	onPullDownRefresh() {
			  uni.request({
			  	url: 'http://127.0.0.1:5000/home',
			  	method: 'GET',
			  	data: {},
			  	header: {
			  		'custom-header': 'hello' //自定义请求头信息
			  	},
			  	success: res => {
			  		this.course = res.data;
			  	}
			  });
		console.log('refresh');
		setTimeout(function() {
			uni.stopPullDownRefresh();
		}, 1000);
	},
	methods: {
		...mapActions('home', ['getHomeData']),
		openinfo(e) {
			console.log(e);
			var courseid = e.currentTarget.dataset.courseid;
			uni.navigateTo({
				url: './maininfo?courseid='+courseid,
			});
		},
	}
};
</script>

<style>
page {
	width: 100%;
	background-color: #ebebeb;
}
</style>
<style lang="scss" scoped>
@function realSize($args) {
	@return $args / 1.5;
}

.page_edu {
	width: 100%;
}

.haibao {
	text-align: center;
	width: 100%;
}
.haibao swiper {
	height: 400upx;
}
.slide-image {
	width: 100%;
	height: 400upx;
}

.page_edu_header {
	// padding-top: var(--status-bar-height);
	// background-color: #0bc99d;
	// background-color: #0faeff;
	width: 100%;
	// height: realSize(415px);
}

.page_content {
	width: 100%;
	// margin-top: -74px;
}

</style>
