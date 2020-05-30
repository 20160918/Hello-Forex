<template>
	<view v-if="!hasLogin" style="text-align: center;height: 400upx;margin-top: 200upx;width: 100%;font-size: 20px;">
		<uni-card title="请先登录" thumbnail="https://img-cdn-qiniu.dcloud.net.cn/uniapp/images/cbd.jpg" is-shadow="true" mode="title">登录后才可进行操作！</uni-card>
	</view>
	<view v-else-if="hasLogin" style="width: 100%;">
		<view class="page_edu">
			<view class="page_edu_header">
				<view class="type">
					<view :class="flag == 0 ? 'select' : 'normal'" id="0" @tap="switchNav">好友推荐</view>
					<view :class="flag == 1 ? 'select' : 'normal'" id="1" @tap="switchNav">好友列表</view>
				</view>
			</view>
			<view class="page_content">
				<view class="container">
					<swiper :current="flag" style="width: 100%;height: 420px;">
						<swiper-item>
							<scroll-view scroll-y="true" style="height: 400px;">
								
								<view style="padding: 5px;border-bottom: #CCCCCC 1px solid"><text>与下列用户拥有共同好友</text></view>
								<view v-for="(item,index) in studentlist" :key="index" :data-index="index">
									<view class="list">
										<view class="flex_col" >
											<view v-if="qcfriendListB.indexOf( item.stu_account ) != -1" class="flex_col" style="width: 100%;display: flex;flex-direction: row;border-bottom: #CCCCCC 1px solid">
												<image v-if="item.stu_avatar == '' || item.stu_avatar ==null" src="../../static/fumou-center-template/header.jpg" class="image" mode="aspectFill"></image>
												<image v-else :src="item.stu_avatar" class="image" mode="aspectFill"></image>
												<view class="flex_grow">
													<view class="flex_col" style="display: flex;flex-direction: row;">
														<view class="flex_grow">{{item.stu_account}}</view>
														<view class="time1">
															<view class="flex_col" style="display: flex;flex-direction: row;">
																<image src="../../static/hm-friends-requests-card/images/img_24029_0_2.png" class="image1" ></image>
																<image src="../../static/hm-friends-requests-card/images/img_24029_0_1.png" class="image1" @tap="add(item.stu_account)"></image>
															</view>
														</view>
													</view>
													<view class="info">拥有共同好友</view>
												</view>
											</view>
										</view>
									</view>
								</view>
								
								<view style="padding: 5px;border-bottom: #CCCCCC 1px solid;"><text>与下列用户兴趣相投</text></view>
								<view v-for="(item,index) in studentlistsecond" :key="index" :data-index="index">
									<view class="list">
										<view class="flex_col" >
											<view v-if="friendListA.indexOf( item.stu_account ) == -1" class="flex_col" style="width: 100%;display: flex;flex-direction: row;border-bottom: #CCCCCC 1px solid">
												<image v-if="item.stu_avatar == '' || item.stu_avatar ==null" src="../../static/fumou-center-template/header.jpg" class="image" mode="aspectFill"></image>
												<image v-else :src="item.stu_avatar" class="image" mode="aspectFill"></image>
												<view class="flex_grow">
													<view class="flex_col" style="display: flex;flex-direction: row;">
														<view class="flex_grow">{{item.stu_account}}</view>
														<view class="time1">
															<view class="flex_col" style="display: flex;flex-direction: row;">
																<image src="../../static/hm-friends-requests-card/images/img_24029_0_2.png" class="image1" ></image>
																<image src="../../static/hm-friends-requests-card/images/img_24029_0_1.png" class="image1" @tap="add(item.stu_account)"></image>
															</view>
														</view>
													</view>
													<view class="info">最感兴趣：{{item.stu_interest}}</view>
												</view>
											</view>
										</view>
									</view>
								</view>
								
								<view style="padding: 5px;border-bottom: #CCCCCC 1px solid;"><text>与下列用户距离相近</text></view>
								<view v-for="(item,index) in studentlist" :key="index" :data-index="index">
									<view class="list">
										<view class="flex_col" >
											<view v-if="ListA.indexOf( item.stu_account ) != -1" class="flex_col" style="width: 100%;display: flex;flex-direction: row;border-bottom: #CCCCCC 1px solid">
												<image v-if="item.stu_avatar == '' || item.stu_avatar ==null" src="../../static/fumou-center-template/header.jpg" class="image" mode="aspectFill"></image>
												<image v-else :src="item.stu_avatar" class="image" mode="aspectFill"></image>
												<view class="flex_grow">
													<view class="flex_col" style="display: flex;flex-direction: row;">
														<view class="flex_grow">{{item.stu_account}}</view>
														<view class="time1">
															<view class="flex_col" style="display: flex;flex-direction: row;">
																<image src="../../static/hm-friends-requests-card/images/img_24029_0_2.png" class="image1" ></image>
																<image src="../../static/hm-friends-requests-card/images/img_24029_0_1.png" class="image1" @tap="add(item.stu_account)"></image>
															</view>
														</view>
													</view>
													<view class="info">距离在200公里以内</view>
												</view>
											</view>
										</view>
									</view>
								</view>

								
								
							</scroll-view>
						</swiper-item>
						<swiper-item>
							<scroll-view scroll-y="true" style="height: 400px;">
								<view class="swiper-box">
									<view class="list"  >
										<!--  @longpress="onLongPress" @tap="listTap"  :data-friendid="item.friend_id" @tap="openinfo"-->
										<view
											class="flex_col"
											
											:class="{ active: pickerUserIndex == index }"
											
											v-for="(item, index) in friendList"
											:key="index"
											:data-friendid="item.stu_account"
											@tap="openinfo"
											>
											<image v-if="item.stu_avatar == '' || item.stu_avatar ==null" src="../../static/fumou-center-template/header.jpg" class="image" mode="aspectFill"></image>
											<image v-else :src="item.stu_avatar" class="image" mode="aspectFill"></image>
											<view class="flex_grow">
												<view v-for="(item, index) in friendList[index].Society" :key="index">
													<view v-if="item.user_id == userName">
														<view class="flex_col">
															<view class="flex_grow">{{ item.friend_id }}</view>
															<!-- <view class="time">{{item.society_time}}</view> -->
														</view>
														<!-- <view class="info">{{ item.society_content }}</view> -->
													</view>
												</view>
											</view>
										</view>
									</view>
									<view class="shade" v-show="showShade" @tap="hidePop">
										<view class="pop" :style="popStyle" :class="{ show: showPop }">
											<view v-for="(item, index) in popButton" :key="index" @tap="pickerMenu" :data-index="index">{{ item }}</view>
										</view>
									</view>
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
import { mapState, mapMutations } from 'vuex';
import uniCard from '@/components/uni-card/uni-card.vue';


export default {
	data() {
		return {
			flag: 0,
			
			friendList: {},
			friendListA:[],
			friendListB:[],
			qcfriendListB:[],
			studentlist:[],
			zh:'',
			
			interest:'',
			studentlistsecond:[],
			
			distanceList:[],
			qcdistanceList:[],
			longitudeA:0,
			latitudeA:0,
			longitudeB:0,
			latitudeB:0,
			account:'',
			avatar: '',
			student:{},
			ListA:[],
			m:0,
			// [
			// 	{
			// 		stu_account:'',
			// 		stu_avatar:'',
			// 		distance:0,
			// 	},
			// ],
			

			/* 窗口尺寸 */
			winSize: {},
			/* 显示遮罩 */
			showShade: false,
			/* 显示操作弹窗 */
			showPop: false,
			/* 弹窗按钮列表 */
			popButton: ['标为关注', '置顶聊天', '删除该聊天'],
			/* 弹窗定位样式 */
			popStyle: '',
			/* 选择的用户下标 */
			pickerUserIndex: -1,

		};
	},
	computed: mapState(['forcedLogin', 'hasLogin', 'userName']),
	onLoad() {
		// this.getListData();
		this.getWindowSize();

		// #ifdef H5
		document.onLong = function(e) {
			var e = e || window.event;
			e.preventDefault();
		};
		// #endif

		uni.showLoading({
			title: '加载中....'
		});
		// 第一种推荐
		uni.request({
			url: 'http://127.0.0.1:5000/friendList',
			method: 'POST',
			data: {
				stu_account: this.userName
			},
			header: {
				'content-type': 'application/json',
				chartset: 'utf-8'
			},
			success: res => {
				// console.log(res.data);
				this.friendList = res.data;
				console.log(this.friendList);

				for (var i = 0; i < res.data.length; i++){
					for (var j = 0; j < res.data[i].Society.length; j++){
						if(this.userName == res.data[i].Society[j].user_id){
							this.friendListA.push(res.data[i].Society[j].friend_id)
						}
					}
				}
				console.log("自己好友列表stu_account数组");
				console.log(this.friendListA);
				// uni.hideLoading();
				
				for (var a = 0; a <this.friendListA.length; a++){
					this.zh = this.friendListA[a]
					uni.request({
						url: 'http://127.0.0.1:5000/firstrecommend',
						method: 'POST',
						data: {
							stu_account: this.zh
							// "record_time":this.currentDate,
							// "record_content":this.commentContent
						},
						header: {
							'content-type': 'application/json',
							chartset: 'utf-8'
						},
						success: res => {
					
							for (var i = 0; i < res.data.length; i++){
								if(this.userName != res.data[i].friend_id && this.friendListA.indexOf(res.data[i].friend_id) == -1 ){
									this.friendListB.push(res.data[i].friend_id)
								}
							}
							for(var i = 0; i <this.friendListB.length; i++){
								if(this.qcfriendListB.indexOf(this.friendListB[i]) == -1 ){
									this.qcfriendListB.push(this.friendListB[i])
								}
							}
							console.log(this.qcfriendListB);
							
						}
					});
				}
			}
		});
		// 距离推荐
		uni.request({
			url: 'http://127.0.0.1:5000/recommend',
			method: 'POST',
			data: {
				stu_account: this.userName
			},
			header: {
				'content-type': 'application/json',
				chartset: 'utf-8'
			},
			success: res => {
				this.studentlist = res.data
				console.log("除自己以外的所有学员");
				console.log(this.studentlist);
				console.log(this.friendListA);
				
				for (var i = 0; i < this.studentlist.length; i++){
					// for(var j = 0; j <this.distanceList.length; j++){
					// 	if(this.qcdistanceList.indexOf(this.distanceList[j]) == -1 ){
					// 		this.qcdistanceList.push(this.distanceList[j])
					// 	}
					// }
					// this.distanceList = this.qcdistanceList
					
					
					if(this.friendListA.indexOf(this.studentlist[i].stu_account) == -1 ){
						this.account = this.studentlist[i].stu_account
						this.avatar = this.studentlist[i].stu_avatar;
						console.log("！！！！！！！！！！！！！！！");
						console.log(this.account);
						uni.request({
							url: 'http://127.0.0.1:5000/secondrecommend1',
							method: 'POST',
							data: {
								stu_account: this.account
							},
							header: {
								'content-type': 'application/json',
								chartset: 'utf-8'
							},
							success: res => {
								this.longitudeB = res.data[0].longitude;
								this.latitudeB = res.data[0].latitude;
								console.log("b的经纬度"+this.longitudeB +' '+this.latitudeB );
								console.log("距离："+this.GetDistance(this.latitudeA, this.longitudeA, this.latitudeB, this.longitudeB));
								
								if(this.GetDistance(this.latitudeA, this.longitudeA, this.latitudeB, this.longitudeB) <= 1000){
									
									// var student = new Object();
									this.student.stu_account =  res.data[0].stu_account; 
									this.ListA.push(res.data[0].stu_account);
									// this.distanceList.push(this.student.stu_account);
									// this.student.stu_account =  this.account; 
									console.log("11111111111111111111111111:"+this.student.stu_account);
									this.student.stu_avatar = res.data[0].stu_avatar;
									// this.distanceList.push(this.student.stu_avatar);
									// this.student.stu_avatar = this.avatar;
									console.log("22222222222222222222222222:"+this.student.stu_avatar );
									this.student.distance = this.GetDistance(this.latitudeA, this.longitudeA, this.latitudeB, this.longitudeB);
									// this.distanceList.push(this.student.distance);
									console.log("33333333333333333333333333:"+this.student.distance );
									console.log(this.student);
									// this.distanceList[m] = this.student;
									this.distanceList.push(this.student);
									// this.m = this.m + 1
									
									// console.log("和自己距离相近的");
									// console.log(this.distanceList);
								}
							}
						});
						
					}
				}
				console.log("和自己距离相近的");
				console.log(this.distanceList);
				console.log(this.ListA);
				uni.hideLoading();
			}
		});
		// 兴趣推荐
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
				console.log(res.data[0].stu_interest);
				this.interest = res.data[0].stu_interest;
				this.longitudeA = res.data[0].longitude;
				this.latitudeA = res.data[0].latitude;
				console.log(this.interest);
				console.log(this.longitudeA);
				console.log(this.latitudeA);
				
				uni.request({
					url: 'http://127.0.0.1:5000/secondrecommend2',
					method: 'POST',
					data: {
						stu_account: this.userName,
						stu_interest: this.interest,
					},
					header: {
						'content-type': 'application/json',
						chartset: 'utf-8'
					},
					success: res => {
						console.log("测试++++++++++++++++++++++++++++++++++++");
						console.log(res.data);
						this.studentlistsecond = res.data;
						console.log("除自己以外的和自己兴趣相投的所有学生");
						console.log(this.studentlistsecond);
					}
				});
			}
		});
	},
	onPullDownRefresh() {
		     this.friendListA = [];
			 this.friendListB = [];
			 this.qcfriendListB = [];
			 // 第一种推荐
			 uni.request({
			 	url: 'http://127.0.0.1:5000/friendList',
			 	method: 'POST',
			 	data: {
			 		stu_account: this.userName
			 		// "record_time":this.currentDate,
			 		// "record_content":this.commentContent
			 	},
			 	header: {
			 		'content-type': 'application/json',
			 		chartset: 'utf-8'
			 	},
			 	success: res => {
			 		// console.log(res.data);
			 		this.friendList = res.data;
			 		console.log(this.friendList);
			 
			 		for (var i = 0; i < res.data.length; i++){
			 			for (var j = 0; j < res.data[i].Society.length; j++){
			 				if(this.userName == res.data[i].Society[j].user_id){
			 					this.friendListA.push(res.data[i].Society[j].friend_id)
			 				}
			 			}
			 		}
			 		console.log("自己好友列表stu_account数组");
			 		console.log(this.friendListA);
			 		// uni.hideLoading();
			 		
			 		for (var a = 0; a <this.friendListA.length; a++){
			 			this.zh = this.friendListA[a]
			 			uni.request({
			 				url: 'http://127.0.0.1:5000/firstrecommend',
			 				method: 'POST',
			 				data: {
			 					stu_account: this.zh
			 					// "record_time":this.currentDate,
			 					// "record_content":this.commentContent
			 				},
			 				header: {
			 					'content-type': 'application/json',
			 					chartset: 'utf-8'
			 				},
			 				success: res => {
			 			
			 					for (var i = 0; i < res.data.length; i++){
			 						if(this.userName != res.data[i].friend_id && this.friendListA.indexOf(res.data[i].friend_id) == -1 ){
			 							this.friendListB.push(res.data[i].friend_id)
			 						}
			 					}
								for(var i = 0; i <this.friendListB.length; i++){
									if(this.qcfriendListB.indexOf(this.friendListB[i]) == -1 ){
										this.qcfriendListB.push(this.friendListB[i])
									}
								}
								console.log(this.qcfriendListB);
								uni.stopPullDownRefresh();
			 				}
			 			});
			 		}
			 	}
			 });
			 // 距离推荐
			 uni.request({
			 	url: 'http://127.0.0.1:5000/recommend',
			 	method: 'POST',
			 	data: {
			 		stu_account: this.userName
			 	},
			 	header: {
			 		'content-type': 'application/json',
			 		chartset: 'utf-8'
			 	},
			 	success: res => {
			 		this.studentlist = res.data
			 		console.log("除自己以外的所有学员");
			 		console.log(this.studentlist);
			 		console.log(this.friendListA);
			 		
			 		for (var i = 0; i < this.studentlist.length; i++){
			 			// for(var j = 0; j <this.distanceList.length; j++){
			 			// 	if(this.qcdistanceList.indexOf(this.distanceList[j]) == -1 ){
			 			// 		this.qcdistanceList.push(this.distanceList[j])
			 			// 	}
			 			// }
			 			// this.distanceList = this.qcdistanceList
			 			
			 			
			 			if(this.friendListA.indexOf(this.studentlist[i].stu_account) == -1 ){
			 				this.account = this.studentlist[i].stu_account
			 				this.avatar = this.studentlist[i].stu_avatar;
			 				console.log("！！！！！！！！！！！！！！！");
			 				console.log(this.account);
			 				uni.request({
			 					url: 'http://127.0.0.1:5000/secondrecommend1',
			 					method: 'POST',
			 					data: {
			 						stu_account: this.account
			 					},
			 					header: {
			 						'content-type': 'application/json',
			 						chartset: 'utf-8'
			 					},
			 					success: res => {
			 						this.longitudeB = res.data[0].longitude;
			 						this.latitudeB = res.data[0].latitude;
			 						console.log("b的经纬度"+this.longitudeB +' '+this.latitudeB );
			 						console.log("距离："+this.GetDistance(this.latitudeA, this.longitudeA, this.latitudeB, this.longitudeB));
			 						
			 						if(this.GetDistance(this.latitudeA, this.longitudeA, this.latitudeB, this.longitudeB) <= 200){
			 							
			 							// var student = new Object();
			 							this.student.stu_account =  res.data[0].stu_account; 
			 							this.ListA.push(res.data[0].stu_account);
			 							// this.distanceList.push(this.student.stu_account);
			 							// this.student.stu_account =  this.account; 
			 							console.log("11111111111111111111111111:"+this.student.stu_account);
			 							this.student.stu_avatar = res.data[0].stu_avatar;
			 							// this.distanceList.push(this.student.stu_avatar);
			 							// this.student.stu_avatar = this.avatar;
			 							console.log("22222222222222222222222222:"+this.student.stu_avatar );
			 							this.student.distance = this.GetDistance(this.latitudeA, this.longitudeA, this.latitudeB, this.longitudeB);
			 							// this.distanceList.push(this.student.distance);
			 							console.log("33333333333333333333333333:"+this.student.distance );
			 							console.log(this.student);
			 							// this.distanceList[m] = this.student;
			 							this.distanceList.push(this.student);
			 							// this.m = this.m + 1
			 							
			 							// console.log("和自己距离相近的");
			 							// console.log(this.distanceList);
			 						}
			 					}
			 				});
			 				
			 			}
			 		}
			 		console.log("和自己距离相近的");
			 		console.log(this.distanceList);
			 		console.log(this.ListA);
			 	}
			 });
			 // 兴趣推荐
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
			 		console.log(res.data[0].stu_interest);
			 		this.interest = res.data[0].stu_interest;
			 		this.longitudeA = res.data[0].longitude;
			 		this.latitudeA = res.data[0].latitude;
			 		console.log(this.interest);
			 		console.log(this.longitudeA);
			 		console.log(this.latitudeA);
			 		
			 		uni.request({
			 			url: 'http://127.0.0.1:5000/secondrecommend2',
			 			method: 'POST',
			 			data: {
			 				stu_interest: this.interest,
			 				stu_account: this.userName
			 			},
			 			header: {
			 				'content-type': 'application/json',
			 				chartset: 'utf-8'
			 			},
			 			success: res => {
			 				this.studentlistsecond = res.data;
			 				console.log("除自己以外的和自己兴趣相投的所有学生");
			 				console.log(this.studentlistsecond);
			 			}
			 		});
			 	}
			 });
		// setTimeout(function() {
		// 	uni.stopPullDownRefresh();
		// }, 1000);
	},
	methods: {
		openinfo(e) {
			// console.log(e);
			var friendid = e.currentTarget.dataset.friendid;
			uni.navigateTo({
				url:'./social-chat?friendid='+friendid,
			});
		},
		// 方法定义 lat,lng 调用 return的距离单位为km
		GetDistance: function ( lat1,  lng1,  lat2,  lng2){
		    var radLat1 = lat1*Math.PI / 180.0;
		    var radLat2 = lat2*Math.PI / 180.0;
		    var a = radLat1 - radLat2;
		    var  b = lng1*Math.PI / 180.0 - lng2*Math.PI / 180.0;
		    var s = 2 * Math.asin(Math.sqrt(Math.pow(Math.sin(a/2),2) +
		    Math.cos(radLat1)*Math.cos(radLat2)*Math.pow(Math.sin(b/2),2)));
		    s = s *6378.137 ;// EARTH_RADIUS;
		    s = Math.round(s * 10000) / 10000;
		    return s;
		},
		
		add:function(e){
			console.log(e);
			uni.request({
				url: 'http://127.0.0.1:5000/addfriend',
				method: 'POST',
				data: {
					user_id: this.userName,
					friend_id: e
				},
				header: {
					'content-type': 'application/json',
					chartset: 'utf-8'
				},
				success: res => {
					if(res.data=='1'){
						uni.showToast({
							title: '添加好友成功！',
							icon: 'none',
							duration: 1000
						});
						uni.startPullDownRefresh();
						this.flag = 1;
					}
				}
			});
		},
		switchNav: function(e) {
			var id = e.target.id;
			var page = this;

			if (this.flag == id) {
				return false;
			} else {
				this.flag = id;
			}
		},
		/* 列表触摸事件 以及打开聊天界面*/
		listTap() {
			/* 因弹出遮罩问题，所以需要在遮罩弹出的情况下阻止列表事件的触发 */
			if (this.showShade) {
				return;
			}

			console.log('列表触摸事件触发');
		},
		/* 获取窗口尺寸 */
		getWindowSize() {
			uni.getSystemInfo({
				success: res => {
					this.winSize = {
						witdh: res.windowWidth,
						height: res.windowHeight
					};
				}
			});
		},
		/* 长按监听 */
		onLongPress(e) {
			let [touches, style, index] = [e.touches[0], '', e.currentTarget.dataset.index];

			/* 因 非H5端不兼容 style 属性绑定 Object ，所以拼接字符 */
			if (touches.clientY > this.winSize.height / 2) {
				style = `bottom:${this.winSize.height - touches.clientY}px;`;
			} else {
				style = `top:${touches.clientY}px;`;
			}
			if (touches.clientX > this.winSize.witdh / 2) {
				style += `right:${this.winSize.witdh - touches.clientX}px`;
			} else {
				style += `left:${touches.clientX}px`;
			}

			this.popStyle = style;
			this.pickerUserIndex = Number(index);
			this.showShade = true;
			this.$nextTick(() => {
				setTimeout(() => {
					this.showPop = true;
				}, 10);
			});
		},
		/* 隐藏弹窗 */
		hidePop() {
			this.showPop = false;
			this.pickerUserIndex = -1;
			setTimeout(() => {
				this.showShade = false;
			}, 250);
		},
		/* 选择菜单 */
		pickerMenu(e) {
			let index = Number(e.currentTarget.dataset.index);
			console.log(`第${this.pickerUserIndex + 1}个用户,第${index + 1}个按钮`);
			// 在这里开启你的代码秀

			uni.showToast({
				title: `第${this.pickerUserIndex + 1}个用户,第${index + 1}个按钮`,
				icon: 'none',
				mask: true,
				duration: 600
			});

			/* 
			 因为隐藏弹窗方法中会将当前选择的用户下标还原为-1,
			 如果行的菜单方法存在异步情况，请在隐藏之前将该值保存，或通过参数传入异步函数中
			 */
			this.hidePop();
		}
	}
};
</script>

<style lang="scss" scoped>
@import './society.css';

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

.swiper-box {
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

/* 列式弹性盒子 */
.flex_col {
	display: flex;
	flex-direction: row;
	flex-wrap: nowrap;
	justify-content: flex-start;
	align-items: center;
	align-content: center;
}

/* 弹性盒子弹性容器 */
.flex_col .flex_grow {
	width: 0;
	-webkit-box-flex: 1;
	-ms-flex-positive: 1;
	flex-grow: 1;
}

.flex_row .flex_grow {
	-webkit-box-flex: 1;
	-ms-flex-positive: 1;
	flex-grow: 1;
}

/* 弹性盒子允许换行 */
.flex_col.flex_wrap {
	-ms-flex-wrap: wrap;
	flex-wrap: wrap;
}

/* 列表 */
.list {
	background-color: #fff;
	font-size: 28upx;
	color: #333;
	user-select: none;
	touch-callout: none;

	& > view {
		padding: 24upx 30upx;
		position: relative;

		&:active,
		&.active {
			background-color: #f3f3f3;
		}

		image {
			height: 80upx;
			width: 80upx;
			border-radius: 4px;
			margin-right: 20upx;
		}

		& > view {
			line-height: 40upx;

			.time,
			.info {
				color: #999;
				font-size: 24upx;
			}

			.time {
				width: 300upx;
				text-align: right;
				font-size: 20upx;
			}
			.time1 {
				width: 200upx;
				text-align: right;
				font-size: 20upx;
			}

			.info {
				overflow: hidden;
				text-overflow: ellipsis;
				white-space: nowrap;
			}
		}
	}

	& > view:not(:first-child) {
		margin-top: 1px;

		&::after {
			content: '';
			display: block;
			height: 0;
			border-top: #ccc solid 1px;
			width: 620upx;
			position: absolute;
			top: -1px;
			right: 0;
			transform: scaleY(0.5); /* 1px像素 */
		}
	}
}

/* 遮罩 */
.shade {
	position: fixed;
	z-index: 100;
	top: 0;
	right: 0;
	bottom: 0;
	left: 0;
	-webkit-touch-callout: none;

	.pop {
		position: fixed;
		z-index: 101;
		width: 200upx;
		box-sizing: border-box;
		font-size: 28upx;
		text-align: left;
		color: #333;
		background-color: #fff;
		box-shadow: 0 0 5px rgba(0, 0, 0, 0.5);
		line-height: 80upx;
		transition: transform 0.15s ease-in-out 0s;
		user-select: none;
		-webkit-touch-callout: none;
		transform: scale(0, 0);

		&.show {
			transform: scale(1, 1);
		}

		& > view {
			padding: 0 20upx;
			overflow: hidden;
			text-overflow: ellipsis;
			white-space: nowrap;
			user-select: none;
			-webkit-touch-callout: none;

			&:active {
				background-color: #f3f3f3;
			}
		}
	}
}
.image1{
	margin-left: 9.74rpx;
	border-radius: 38.96rpx;
	background-color: #d9f2ec;
	width: 77.92rpx;
	height: 77.92rpx;
}

.ec-canvas {
	width: 100%;
	height: 100%;
}
</style>
