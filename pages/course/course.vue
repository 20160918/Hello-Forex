<template>
	<view v-if="!hasLogin" style="text-align: center;height: 400upx;margin-top: 200upx;width: 100%;font-size: 20px;">
		<uni-card title="请先登录" thumbnail="https://img-cdn-qiniu.dcloud.net.cn/uniapp/images/cbd.jpg" is-shadow="true" mode="title">登录后才可进行操作！</uni-card>
	</view>
	<view v-else-if="hasLogin" style="width: 100%;">
		<view class="page_edu">
			<view class="page_edu_header">
				<view class="type">
					<view :class="flag == 0 ? 'select' : 'normal'" id="0" @tap="switchNav">课程通知</view>
					<view :class="flag == 1 ? 'select' : 'normal'" id="1" @tap="switchNav">课程点播</view>
				</view>
			</view>
			<view class="page_content">
				<view class="container">
					<swiper :current="flag" style="width: 100%;height: 420px;">
						<swiper-item>
							<scroll-view scroll-y="true" style="height: 400px;">
								<view style="margin-top: 10px;">
									<view v-for="(item, index) in mycourse" :key="index">
										<an-text-area-tip :title="item.course_name + '通知'" lineColor="#0a98d5" titleBgColor="#f00" color="#fff">
											<text>{{ item.course_notice }}</text>
										</an-text-area-tip>
									</view>
								</view>
							</scroll-view>
						</swiper-item>
						<swiper-item>
							<scroll-view scroll-y="true" style="height: 400px;">
								<view class="container">
									<view style="padding: 12px;">
										<image src="/static/img/课程%20学业.png" style="width:30px;height:30px;margin-right:10px;"></image>
										<text style="font-size: 24px;font-weight: 500;">我的课程</text>
									</view>
									<block v-for="(item, index) in mycourse" :key="index">
										<view class="hr"></view>
										<view :data-courseid="item.course_id" @tap="openinfo">
											<uni-list>
												<uni-list-item :note="item.course_studyNum + '人正在学习'" :showArrow="true">
													<view style="display: flex;">
														<text style="padding: 20px 3px;width: 70%;">{{ item.course_name }}</text>
														<image style="width: 80px;height: 80px;" src="/static/img/home-icon.png" mode="widthFix"></image>
													</view>
												</uni-list-item>
											</uni-list>
										</view>
									</block>
								</view>
							</scroll-view>
						</swiper-item>
					</swiper>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
import uniList from '@dcloudio/uni-ui/lib/uni-list/uni-list.vue';
import uniListItem from '@dcloudio/uni-ui/lib/uni-list-item/uni-list-item.vue';
import anTextAreaTip from '@/components/an-textarea-tip/an-textarea-tip';
import { mapState, mapMutations } from 'vuex';
import uniCard from '@/components/uni-card/uni-card.vue';

export default {
	computed: mapState(['forcedLogin', 'hasLogin', 'userName']),
	components: {
		uniList,
		uniListItem,
		anTextAreaTip,
		uniCard
	},

	data() {
		return {
			flag: 1,
			songs: [],
			users: {},
			course: {},
			mycourse: {}
		};
	},
	onLoad: function() {
		// setTimeout(function() {
		// 	console.log('start pulldown');
		// }, 1000);
		// uni.startPullDownRefresh();

		uni.showLoading({
			title: '加载中....'
		});
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
				uni.hideLoading();
			}
		});
	},

	onPullDownRefresh() {
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
				// uni.hideLoading();
			}
		});
		console.log('refresh');
		setTimeout(function() {
			uni.stopPullDownRefresh();
		}, 1000);
	},
	methods: {
		switchNav: function(e) {
			var id = e.target.id;
			var page = this;

			if (this.flag == id) {
				return false;
			} else {
				this.flag = id;
			}
		},
		openinfo: function(e) {
			var courseid = e.currentTarget.dataset.courseid;
			uni.navigateTo({
				url: './courseinfo?courseid=' + courseid
			});
		}
	}
};
</script>

<style lang="scss" scoped>
@function realSize($args) {
	@return $args / 1.5;
}
.page_edu {
	width: 100%;
	height: 100%;
}
.page_edu_header {
	width: 100%;
}
.page_content {
	width: 100%;
}
.type {
	display: flex;
	flex-direction: row;
}
.select {
	font-size: 16px;
	color: #1797d4;
	width: 50%;
	text-align: center;
	height: 30px;
	line-height: 30px;
	border-bottom: 5rpx solid #1797d4;
}
.normal {
	width: 50%;
	font-size: 16px;
	text-align: center;
	height: 30px;
	line-height: 30px;
}
</style>
