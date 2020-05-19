<template>
	<view v-if="!hasLogin" style="text-align: center;height: 400upx;margin-top: 200upx;width: 100%;font-size: 20px;">
		<uni-card title="请先登录" thumbnail="https://img-cdn-qiniu.dcloud.net.cn/uniapp/images/cbd.jpg" is-shadow='true' mode='title'>
		    登录后才可进行操作！
		</uni-card>
	</view>
	<view v-else-if="hasLogin" style="width: 100%;">
		
		<view class="page_edu">
			<view class="page_edu_header">
				<view class="type">
					<view :class="flag == 0 ? 'select' : 'normal'" id="0" @tap="switchNav">今日打卡</view>
					<view :class="flag == 1 ? 'select' : 'normal'" id="1" @tap="switchNav">打卡记录</view>
					<view :class="flag == 2 ? 'select' : 'normal'" id="2" @tap="switchNav">打卡排行</view>
				</view>
			</view>
			<view class="page_content">
				<view class="container">
					<swiper :current="flag" style="width: 100%;height: 420px;">
						<swiper-item>
							<scroll-view scroll-y="true" style="height: 400px;">
								<!-- 打卡日历页面 -->
								<view class="all">
									<view class="bar">
										<!-- 上一个月 -->
										<view class="previous" @click="handleCalendar(0)">
											<button class="barbtn" v-if="langType == 'ch'">上一月</button>
											<button class="barbtn" v-else>Last</button>
										</view>
										<!-- 显示年月 -->
										<view class="date">{{ cur_year || '--' }} 年 {{ cur_month || '--' }} 月</view>
										<!-- 下一个月 -->
										<view class="next" @click="handleCalendar(1)">
											<button class="barbtn" v-if="langType == 'ch'">下一月</button>
											<button class="barbtn" v-else>Next</button>
										</view>
									</view>
									<!-- 显示星期 -->
									<view class="week" v-if="langType == 'ch'">
										<view v-for="(item, index) in weeks_ch" :key="index">{{ item }}</view>
									</view>
									<view class="week" v-else>
										<view v-for="(item, index) in weeks_en" :key="index">{{ item }}</view>
									</view>
									<view class="myDateTable">
										<view v-for="(item, j) in days" :key="j" class="dateCell">
											<view v-if="item.date == undefined || item.date == null" class="cell"><text :decode="true">&nbsp;&nbsp;</text></view>
											<view v-else>
												<!-- 已签到日期 -->
												<view v-if="item.isSign == true" class="cell greenColor bgWhite  ">
													<text>{{ item.date }}</text>
												</view>
												<!-- 漏签 -->
												<view
													@click="clickSignUp(item.date, 0)"
													class="cell redColor bgGray"
													v-else-if="
														cur_year < toYear || (cur_year == toYear && cur_month < toMonth) || (cur_year == toYear && cur_month == toMonth && item.date < today)
													"
												>
													<!-- 小程序不兼容这个 v-else-if="(new Date(cur_year+'-'+cur_month+'-'+item.date))<(new Date())"> -->
													<text>{{ item.date }}</text>
												</view>
												<!-- 今日未签到-->
												<view @click="clickSignUp(item.date, 1, 'show')" class="cell whiteColor bgBlue" v-else-if="item.date == today && cur_month == toMonth && cur_year == toYear">
													<text>签到</text>
												</view>
												<!-- 当前日期之后 -->
												<view class="whiteColor cell" v-else>
													<text>{{ item.date }}</text>
												</view>
											</view>
										</view>
									</view>
									<!-- <view class="TipArea ">
										Tip:
										<p>打卡成功后需要你<view class="impTip">同步数据给数据库，切换月或重新进入</view>
										页面再向数据库读取记录(不会的建议参考我的Demo,而不是单独下个组件过来瞎折腾)。本地打卡不做任何记录，<view class="impTip">仅仅模拟成功</view>,请勿无脑喷，谢谢！
										</p>
									</view> -->
									<view class="count">
										<text>截至目前，已坚持打卡</text>
										<view class="daynumber">
											<text class="number">{{ cardDay }}</text>
											<text class="day">天</text>
										</view>
									
										<!-- <view>
											本月累积打卡
											<text class="monthSum">{{ signData.length }}</text>
											天
										</view> -->
										<text>请再接再厉，继续努力!</text>
									</view>
									
									<ygc-comment ref="ygcComment" :placeholder="'打卡记录'" @pubComment="pubComment"></ygc-comment>
								</view>
							</scroll-view>
						</swiper-item>
						<swiper-item>
							<scroll-view scroll-y="true" style="height: 400px;">
								<view class="uni-padding-wrap">
									<view class="uni-comment">
										
										<view v-for="(item1,index1) in record" :key="index1">
											<view  v-for="(item,index) in record[index1].Record" :key="index" >
												<view class="uni-comment-list">
													<view class="uni-comment-face">
														<image v-if="item1.stu_avatar=='' || avatar== null" src="../../static/fumou-center-template/header.jpg" class="image" mode="widthFix"></image>
														<image v-else :src="item1.stu_avatar" class="image" mode="widthFix"></image>
													</view>
													<view class="uni-comment-body">
														<view class="uni-comment-top"><text>{{item1.stu_account}}</text></view>
														<view class="uni-comment-date"><text>{{item.record_time}}</text></view>
														<view class="uni-comment-content">{{item.record_content}}</view>
														<view class="uni-comment-date">
															<view class="uni-comment-replay-btn">评论</view>
															<view class="uni-comment-replay-btn">点赞</view>
														</view>
													</view>
												</view>
												<view class="hr"></view>
											</view>
										</view>
									
									</view>
								</view>
							</scroll-view>
						</swiper-item>
						<swiper-item>
							
							<scroll-view scroll-y="true" style="height: 400px;">
								
								<view style="padding: 12px; height: 150px;text-align: center;">
									<view >
										<image v-if="avatar=='' || avatar== null" src="../../static/fumou-center-template/header.jpg" style="width:60px;height:60px;text-align: center;"  mode="aspectFill"></image>
										<image v-else :src="avatar" style="width:30px;height:50px;text-align: center;"  mode="aspectFill"></image>
									</view>
									<view ><text style="font-size: 20px;font-weight: 500;text-align: center;color: #0a98d5;">{{userName}}</text></view>
									<view ><text style="font-size: 24px;font-weight: 500;text-align: center;;"><text style="color: #DE211D;padding: 5px;">{{cardDay}}</text>天</text></view>
									<view ><text style="font-size: 15px;font-weight: 500;text-align: center;">第<text style="color: #FFC107;font-size: 20px;padding: 5px;">{{rank}}</text>名</text></view>
								</view>
								<view class="hr"></view>
								
								<view class="list">
									<view class="flex_col" :class="{'active':pickerUserIndex==index}" v-for="(item,index) in sort" :key="index" :data-index="index">
										<view class="image">{{index+1}}</view>
										<image v-if="item.stu_avatar=='' || avatar== null" src="../../static/fumou-center-template/header.jpg" class="image" mode="aspectFill"></image>
										<image v-else :src="item.stu_avatar" class="image" mode="aspectFill"></image>
										<view class="flex_grow">
											<view class="flex_col">
												<view class="flex_grow" style="color: #0a98d5;">{{item.stu_account}}</view>
												<view class="time"><text style="color: #DE211D;padding-right: 5px;">{{item.stu_cardDay}}</text>天</view>
											</view>
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
import ygcComment from '@/components/ygc-comment/ygc-comment.vue';
import uniCard from '@/components/uni-card/uni-card.vue'

export default {
	components: {ygcComment},
	data() {
		return {
			flag: 0,
			days: [],
			SignUp: [],
			cur_year: 0, //当前选的年
			cur_month: 0, //当前选的月
			today: parseInt(new Date().getDate()), //本日
			toMonth: parseInt(new Date().getMonth() + 1), //本月
			toYear: parseInt(new Date().getFullYear()), //本年
			weeks_ch: ['日', '一', '二', '三', '四', '五', '六'],
			weeks_en: ['Sun', 'Mon', 'Tues', 'Wed', 'Thur', 'Fri', 'Sat'],

			commentContent: '',
			title: '打卡记录界面',
			currentDate: '',
			record:{},
			sort:{},
			cardDay:0,
			rank:0,
			avatar:'',
			cardtime:[],
			newcardtime:[]
			

		};
	},
	computed: mapState(['forcedLogin', 'hasLogin', 'userName']),
	props: {
		sendYear: {
			type: Number,
			default: new Date().getFullYear()
		},
		sendMonth: {
			type: Number,
			default: new Date().getMonth() + 1
		},
		dataSource: {
			//已签到的数据源
			type: Array,
			default: () => {
				return [];
			}
		},
		langType: {
			//只是示例一个翻译而已，要想所有都翻译自己可以再加加
			type: String,
			default: 'ch'
		}
	},
	created() {
		this.cur_year = this.sendYear;
		this.cur_month = this.sendMonth;
		this.dataSource = this.cardtime;
		this.SignUp = this.dataSource;

		this.calculateEmptyGrids(this.cur_year, this.cur_month);
		this.calculateDays(this.cur_year, this.cur_month);
		this.onJudgeSign();
	},
	watch: {
		dataSource: 'onResChange'
	},
    onLoad() {
		
		var myDate = new Date();
		this.currentDate = myDate.toLocaleString( );
		
		uni.showLoading({
			title:"加载中....",
		});
		
		uni.request({
			url: 'http://127.0.0.1:5000/record',
			method: 'GET',
			data: {},
			header: {
				'custom-header': 'hello' //自定义请求头信息
			},
			success: res => {
				this.record = res.data;
				uni.hideLoading();
			}
		});
		uni.request({
			url: 'http://127.0.0.1:5000/sort',
			method: 'GET',
			data: {},
			header: {
				'custom-header': 'hello' //自定义请求头信息
			},
			success: res => {
				this.sort = res.data;
				for (var i = 0; i < this.sort.length; i++){
					if(this.userName==this.sort[i].stu_account){
						this.cardDay = this.sort[i].stu_cardDay,
						this.rank = i+1,
						this.avatar =  this.sort[i].stu_avatar
					}
				}
			}
		});
		uni.request({
			url: 'http://127.0.0.1:5000/cardtime',
			method: 'POST',
			data: {
				stu_account:this.userName
			},
			header: {
				'content-type': 'application/json',
				 chartset: 'utf-8'
			},
			success: res => {
				for(var i = 0; i < res.data.length; i++){
					this.cardtime.push(this.getTime(res.data[i].card_time))
				}
				
			}
		});
	  },
	  onPullDownRefresh() {
		  uni.request({
		  	url: 'http://127.0.0.1:5000/record',
		  	method: 'GET',
		  	data: {},
		  	header: {
		  		'custom-header': 'hello' //自定义请求头信息
		  	},
		  	success: res => {
		  		this.record = res.data;
		  	}
		  });
		  uni.request({
		  	url: 'http://127.0.0.1:5000/sort',
		  	method: 'GET',
		  	data: {},
		  	header: {
		  		'custom-header': 'hello' //自定义请求头信息
		  	},
		  	success: res => {
		  		this.sort = res.data;
		  		for (var i = 0; i < this.sort.length; i++){
		  			if(this.userName==this.sort[i].stu_account){
		  				this.cardDay = this.sort[i].stu_cardDay,
		  				this.rank = i+1,
		  				this.avatar =  this.sort[i].stu_avatar
		  			}
		  		}
		  	}
		  });
		  uni.request({
		  	url: 'http://127.0.0.1:5000/cardtime',
		  	method: 'POST',
		  	data: {
		  		stu_account:this.userName
		  	},
		  	header: {
		  		'content-type': 'application/json',
		  		 chartset: 'utf-8'
		  	},
		  	success: res => {
				this.cardtime = [];
		  		for(var i = 0; i < res.data.length; i++){
		  			this.cardtime.push(this.getTime(res.data[i].card_time))
		  		}
				this.dataSource = this.cardtime;
				this.SignUp = this.datasource;
		  		
		  	}
		  });
	  	console.log('refresh');
	  	setTimeout(function() {
	  		uni.stopPullDownRefresh();
	  	}, 1000);
	  },

	methods: {
		getTime:function(time){
		
		var date = new Date(time),
		year = date.getFullYear(),
		month = date.getMonth() + 1,
		day = date.getDate(),
		hour = date.getHours() < 10 ? "0" + date.getHours() : date.getHours(),
		minute = date.getMinutes() < 10 ? "0" + date.getMinutes() : date.getMinutes(),
		second = date.getSeconds() < 10 ? "0" + date.getSeconds() : date.getSeconds();
		month >= 1 && month <= 9 ? (month = "0" + month) : "";
		day >= 0 && day <= 9 ? (day = "0" + day) : "";
		var timer = year + '-' + month + '-' + day;
		return timer;
		},
		// getTime1:function(time){
		
		// var date = new Date(time),
		// year = date.getFullYear(),
		// month = date.getMonth() + 1,
		// day = date.getDate(),
		// hour = date.getHours() < 10 ? "0" + date.getHours() : date.getHours(),
		// minute = date.getMinutes() < 10 ? "0" + date.getMinutes() : date.getMinutes(),
		// second = date.getSeconds() < 10 ? "0" + date.getSeconds() : date.getSeconds();
		// month >= 1 && month <= 9 ? (month = "0" + month) : "";
		// day >= 0 && day <= 9 ? (day = "0" + day) : "";
		// var timer = year + '-' + month + '-' + day+ ' ' + hour + ':' + minute + ':' + second;
		// return timer;
		// },
		
		// 切换横向标签
		switchNav: function(e) {
			var id = e.target.id;
			var page = this;

			if (this.flag == id) {
				return false;
			} else {
				this.flag = id;
			}
		},
		// 获取当月共多少天
		getThisMonthDays(year, month) {
			return new Date(year, month, 0).getDate();
		},
		// 获取当月第一天星期几
		getFirstDayOfWeek(year, month) {
			return new Date(Date.UTC(year, month - 1, 1)).getDay();
		},
		// 计算当月1号前空了几个格子，把它填充在days数组的前面
		calculateEmptyGrids(year, month) {
			//计算每个月时要清零
			this.days = [];
			const firstDayOfWeek = this.getFirstDayOfWeek(year, month);
			if (firstDayOfWeek > 0) {
				for (let i = 0; i < firstDayOfWeek; i++) {
					var obj = {
						date: null,
						isSign: false
					};
					this.days.push(obj);
				}
			}
		},

		// 绘制当月天数占的格子，并把它放到days数组中
		calculateDays(year, month) {
			const thisMonthDays = this.getThisMonthDays(year, month);
			// this.columnsLen=Math.ceil(thisMonthDays/7);
			// console.log(this.columnsLen);
			for (let i = 1; i <= thisMonthDays; i++) {
				var obj = {
					date: i,
					isSign: false
				};
				this.days.push(obj);
			}
			//console.log(this.days);
		},

		onResChange(newD, oldD) {
			this.SignUp = newD;
			this.onJudgeSign();
		},
		//匹配判断当月与当月哪些日子签到打卡
		onJudgeSign() {
			var signs = this.SignUp;
			var daysArr = this.days;
			for (var i = 0; i < signs.length; i++) {
				var current = new Date(signs[i].replace(/-/g, '/'));
				var year = current.getFullYear();
				var month = current.getMonth() + 1;
				var day = current.getDate();
				// console.log(year+' '+month+' '+day);
				day = parseInt(day);
				for (var j = 0; j < daysArr.length; j++) {
					//年月日相同则打卡成功
					if (year == this.cur_year && month == this.cur_month && daysArr[j].date == day) {
						//&& signs[i].isSign == "今日已打卡"
						// console.log(daysArr[j].date, day);
						daysArr[j].isSign = true;
					}
				}
			}
			this.days = daysArr;
		},

		// 切换控制年月，上一个月，下一个月
		handleCalendar(type) {
			const cur_year = parseInt(this.cur_year);
			const cur_month = parseInt(this.cur_month);
			var newMonth;
			var newYear = cur_year;
			if (type === 0) {
				//上个月
				newMonth = cur_month - 1;
				if (newMonth < 1) {
					newYear = cur_year - 1;
					newMonth = 12;
				}
			} else {
				newMonth = cur_month + 1;
				if (newMonth > 12) {
					newYear = cur_year + 1;
					newMonth = 1;
				}
			}
			this.calculateEmptyGrids(newYear, newMonth);
			this.calculateDays(newYear, newMonth);

			this.cur_year = newYear;
			this.cur_month = newMonth;


			this.onJudgeSign();
			this.$emit('dateChange', this.cur_year + '-' + this.cur_month); //传给调用模板页面去拿新数据
		},

		clickSignUp(date, type, type2) {
			this.$refs.ygcComment.toggleMask(type2);
			uni.request({
				url: 'http://127.0.0.1:5000/updatecardDay',
				method: 'POST',
				data: {
					stu_account: this.userName
				},
				header: {
					'content-type': 'application/json',
					chartset: 'utf-8'
				},
				success: res => {
					if(res.data == "1"){
						console.log("打卡天数增加成功")
					}
				}
			});
			
			//0补签，1当日签到

			var str = '签到';
			if (type == 0) {
				//如果不需要补签功能直接在这阻止不执行后面的代码就行。
				str = '补签';
			}
			uni.showToast({
				title: str + '成功' + date + '号',
				icon: 'success',
				duration: 2000
			});
			// this.SignUp.push(this.cur_year + '-' + this.cur_month + '-' + date); //自动加假数据，写了接口就不用了

			this.$forceUpdate();

			this.$emit('clickChange', this.cur_year + '-' + this.cur_month + '-' + date); //传给调用模板页面
			
			uni.request({
				url: 'http://127.0.0.1:5000/addcardtime',
				method: 'POST',
				data: {
					stu_account: this.userName,
					card_time: this.cur_year + '-' + this.cur_month + '-' + date
				},
				header: {
					'content-type': 'application/json',
					chartset: 'utf-8'
				},
				success: res => {
					if(res.data == "1"){
						console.log("打卡时间增加成功");
						setTimeout(function() {
							console.log('start pulldown');
						}, 1000);
						this.onJudgeSign();
						uni.startPullDownRefresh();
					}
				}
			});

		},

		pubComment(commentContent1) {
			this.$refs.ygcComment.toggleMask();
			console.log(commentContent1);
			this.commentContent = commentContent1;
			var myDate = new Date();
			this.currentDate = myDate.toLocaleString('chinese',{hour12:false}); 
			console.log(this.currentDate);
			console.log(this.userName);
			uni.request({
				url: 'http://127.0.0.1:5000/addRecord',
				method: 'POST',
				data: {
					"stu_account":this.userName,
					"record_time":this.currentDate,
					"record_content":this.commentContent
				},
				header: {
					'content-type': 'application/json',
					'chartset': 'utf-8'
				},
				success: res => {
					console.log(res.data);
					uni.showToast({
						title: '发布成功',
						icon: 'none',
						duration: 1000
					});
				}
			});
			this.$refs.ygcComment.content = '';
		},

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
.content {
	background-color: #ffffff;
}
.type {
	display: flex;
	flex-direction: row;
}
.select {
	font-size: 16px;
	color: #1797d4;
	width: 33%;
	text-align: center;
	height: 30px;
	line-height: 30px;
	border-bottom: 5rpx solid #1797d4;
}
.normal {
	width: 33%;
	font-size: 16px;
	text-align: center;
	height: 30px;
	line-height: 30px;
}
.all {
	margin-top: 20rpx;
}

.all .bar {
	display: flex;
	flex-direction: row;
	justify-content: space-between;
	margin: 30rpx 20rpx;
	padding: 10rpx;
}

.bar .barbtn {
	height: 30px;
	line-height: 30px;
	font-size: 12px;
}

.all .week {
	display: flex;
	flex-direction: row;
	justify-content: space-between;
	padding: 20rpx;
	padding-left: 40rpx;
	padding-right: 40rpx;
	margin: 20rpx;
	border-radius: 10px;
	background-color: #fff;
}
.myDateTable {
	margin: 2.5vw;
	padding: 2vw;
	border-radius: 10px;
	background: linear-gradient(#74aada, #94db98);
}
.myDateTable .dateCell {
	width: 11vw;
	padding: 1vw;
	display: inline-block;
	text-align: center;
	font-size: 16px;
}

.dateCell .cell {
	display: flex;
	border-radius: 50%;
	height: 11vw;
	justify-content: center;
	align-items: center;
}

.whiteColor {
	color: #fff;
}

.greenColor {
	color: #01b90b;
	font-weight: bold;
}

.bgWhite {
	background-color: #fff;
}

.bgGray {
	background-color: rgba(255, 255, 255, 0.42);
}

.bgBlue {
	font-size: 14px;
	background-color: #4b95e6;
}

.redColor {
	color: #ff0000;
}

.TipArea {
	word-break: break-all;
	word-wrap: break-word;

	font-size: 14px;
	padding: 10px;
}
.impTip {
	display: inline-block;
	color: #ff0000;
}
.count .daynumber {
	display: flex;
	flex-direction: row;
	justify-content: center;
}

.count .daynumber .day {
	margin-top: 50rpx;
}

.count {
	margin: 20rpx;
	padding: 30rpx;
	display: flex;
	text-align: center;
	border-radius: 10px;
	flex-direction: column;
	justify-content: center;
	background-color: #fff;
	align-items: center;
}

.count .number {
	color: #fff;
	font-size: 60rpx;
	background-color: #94db98;
	width: 100rpx;
	height: 100rpx;
	border-radius: 50%;
	display: flex;
	flex-direction: column;
	justify-content: center;
	margin: 20rpx;
}

.monthSum {
	color: red;
	font-size: 40rpx;
}

.count text {
	margin: 10rpx;
}
.uni-padding-wrap {
	padding: 30upx;
}

view {
	font-size: 28upx;
}

.uni-comment {
	padding: 5rpx 0;
	display: flex;
	flex-grow: 1;
	flex-direction: column;
}

.hr {
	border-bottom: #000000 1upx solid;
}

.uni-comment-list {
	flex-wrap: nowrap;
	padding: 10rpx 0;
	margin: 10rpx 0;
	width: 100%;
	display: flex;
}

.uni-comment-face {
	width: 70upx;
	height: 70upx;
	border-radius: 100%;
	margin-right: 20upx;
	flex-shrink: 0;
	overflow: hidden;
}

.uni-comment-face image {
	width: 100%;
	border-radius: 100%;
}

.uni-comment-body {
	width: 100%;
}

.uni-comment-top {
	line-height: 1.5em;
	justify-content: space-between;
}

.uni-comment-top text {
	color: #0a98d5;
	font-size: 24upx;
}

.uni-comment-date {
	line-height: 38upx;
	flex-direction: row;
	justify-content: space-between;
	display: flex !important;
	flex-grow: 1;
}

.uni-comment-date view {
	color: #666666;
	font-size: 24upx;
	line-height: 38upx;
}

.uni-comment-content {
	line-height: 1.6em;
	font-size: 28upx;
	padding: 8rpx 0;
}

.uni-comment-replay-btn {
	background: #fff;
	font-size: 24upx;
	line-height: 28upx;
	padding: 5rpx 20upx;
	border-radius: 30upx;
	color: #333 !important;
	margin: 0 10upx;
	border: #C0C4CC 1rpx solid;
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
		font-size: 35upx;
		color: #333;
		user-select: none;
		touch-callout: none;
	
		&>view {
			padding: 24upx 30upx;
			position: relative;
	
			&:active,
			&.active {
				background-color: #f3f3f3;
			}
	
			.image {
				height: 80upx;
				width: 80upx;
				border-radius: 4px;
				margin-right: 20upx;
			}
	
			&>view {
				line-height: 40upx;
	
				.time,
				.info {
					color: #999;
					font-size: 24upx;
				}
	
				.time {
					width: 150upx;
					text-align: right;
				}
	
				.info {
					overflow: hidden;
					text-overflow: ellipsis;
					white-space: nowrap;
				}
			}
		}
	
		&>view:not(:first-child) {
			margin-top: 1px;
	
			&::after {
				content: '';
				display: block;
				height: 0;
				border-top: #CCC solid 1px;
				width: 620upx;
				position: absolute;
				top: -1px;
				right: 0;
				transform:scaleY(0.5);	/* 1px像素 */
			}
		}
	}
</style>
