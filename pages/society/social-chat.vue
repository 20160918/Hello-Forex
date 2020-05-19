<template>
	<view class="content">
		<view class="tips color_fff size_12 align_c" :class="{ 'show':ajax.loading }" @tap="getHistoryMsg">{{ajax.loadText}}</view>
		<view class="box-1" id="list-box">
			<view class="talk-list">
				<view v-for="(item,index) in talkList" :key="index" :id="`msg-${item.chat_id}`">
					<view class="item flex_col" :class=" item.chat_type == 1 ? 'push':'pull' ">
						<!-- <image :src="item.pic" mode="aspectFill" class="pic"></image> -->
						<image src="../../static/fumou-center-template/header.jpg" mode="aspectFill" class="pic"></image>
						<view class="content">{{item.chat_content}}</view>
					</view>
				</view>
			</view>
		</view>
		<view class="box-2">
			<view class="flex_col">
				<view class="flex_grow">
					<input type="text" class="content" v-model="content" @input="bindContentInput" placeholder="请输入聊天内容" placeholder-style="color:#DDD;" :cursor-spacing="6">
				</view>
				<button :disabled="disabled" class="send" @tap="send">发送</button>
			</view>
		</view>
	</view>
</template>

<script>
	import { mapState, mapMutations } from 'vuex';
	export default {
		data() {
			return {
				disabled: true,
				friendid: '',
				arrs: [],
				talkList:[],
				chat_time:'',
				ajax:{
					rows:20,	//每页数量
					page:1,	//页码
					flag:true,	// 请求开关
					loading:true,	// 加载中
					loadText:'正在获取消息'
				},
				content:''
			}
		},
		computed: mapState(['forcedLogin', 'hasLogin', 'userName']),
		onLoad: function(e) {
			this.friendid = e.friendid;
			uni.setNavigationBarTitle({
				title:this.friendid,
				});
		},
		mounted() {
			this.$nextTick(()=>{
				this.getHistoryMsg();
			});
		},
		onPageScroll(e){
			if(e.scrollTop<5){
				this.getHistoryMsg();
			}
		},
		methods: {
			// 获取输入的聊天消息
			bindContentInput(e){
				this.content = e.detail.value;
				if(this.content){
					this.disabled = false
				}else{
					this.disabled = true
				};
			},
			// 获取历史消息
			getHistoryMsg(){
				if(!this.ajax.flag){
					return; //
				}
				// 此处用到 ES7 的 async/await 知识，为使代码更加优美。不懂的请自行学习。
				let get = async ()=>{
					this.hideLoadTips();
					this.ajax.flag = false;
					let data = await this.joinHistoryMsg();
					console.log('----- 模拟数据格式，供参考 -----');
					console.log(data);	// 查看请求返回的数据结构 
					// 获取待滚动元素选择器，解决插入数据后，滚动条定位时使用
					let selector = '';			
					if(this.ajax.page>1){
						// 非第一页，则取历史消息数据的第一条信息元素
						selector = `#msg-${this.talkList[0].chat_id}`;
					}else{
						// 第一页，则取当前消息数据的最后一条信息元素
						selector = `#msg-${data[data.length-1].chat_id}`;
					}
					// 将获取到的消息数据合并到消息数组中
					this.talkList = [...data,...this.talkList];	
					// 数据挂载后执行，不懂的请自行阅读 Vue.js 文档对 Vue.nextTick 函数说明。
					this.$nextTick(()=>{
						this.arrs = [];
						// 设置当前滚动的位置
						this.setPageScrollTo(selector);
						this.hideLoadTips(true);
						if(data.length < this.ajax.rows){
							// 当前消息数据条数小于请求要求条数时，则无更多消息，不再允许请求。
							// 可在此处编写无更多消息数据时的逻辑
						}else{
							this.ajax.page ++;
							// 延迟 200ms ，以保证设置窗口滚动已完成
							setTimeout(()=>{
								this.ajax.flag = true;
							},200)
						}
						
					})
				}
				get();
			},
			// 拼接历史记录消息，正式项目可替换为请求历史记录接口
			joinHistoryMsg(){
				uni.request({
					url: 'http://127.0.0.1:5000/chatcontent',
					method: 'POST',
					data: {
						"user_id":this.userName,
						"friend_id":this.friendid
					},
					header: {
						'content-type': 'application/json',
						'chartset': 'utf-8'
					},
					success: res => {
						console.log(res.data);
						// 通过当前页码及页数，模拟数据内容
						let startIndex = (this.ajax.page-1) * this.ajax.rows;
						let endIndex = startIndex + this.ajax.rows;
						for(let i = startIndex; i < endIndex; i++){
							// let j = res.data.length - i + 1;
							if(i< res.data.length){
								this.arrs.push(res.data[i]);
								console.log('第'+i+'个');
								console.log(res.data[i]);
							}else{
								break;
							}
						}
						console.log("聊天消息！！！！！！！！！！！！！！！");
						this.arrs.reverse();
						console.log(this.arrs);
					}
				});
				// 此处用到 ES6 的 Promise 知识，不懂的请自行学习。
				return new Promise((done,fail)=>{
					// 无数据请求接口，由 setTimeout 模拟，正式项目替换为 ajax 即可。
					setTimeout(()=>{
						// let data = join();
						let data = this.arrs;
						done(data);
					},1500);
				})
			},
			// 设置页面滚动位置
			setPageScrollTo(selector){
				let view = uni.createSelectorQuery().in(this).select(selector);
				view.boundingClientRect((res) => {
					uni.pageScrollTo({
					    scrollTop:res.top - 30,	// -30 为多显示出大半个消息的高度，示意上面还有信息。
					    duration: 0
					});
				}).exec();
			},
			// 隐藏加载提示
			hideLoadTips(flag){
				if(flag){
					this.ajax.loadText = '消息获取成功';
					setTimeout(()=>{
						this.ajax.loading = false;
					},300);
				}else{
					this.ajax.loading = true;
					this.ajax.loadText = '正在获取消息';
				}
			},
			// 发送信息
			send(){
				if(!this.content){
					uni.showToast({
						title:'请输入有效的内容',
						icon:'none'
					})
					return;
				}
				uni.showLoading({
					title:'正在发送'
				})
				var myDate = new Date();
				this.chat_time = myDate.toLocaleString('chinese',{hour12:false}); 
				uni.request({
					url: 'http://127.0.0.1:5000/addchatcontent',
					method: 'POST',
					data: {
						"user_id":this.userName,
						"friend_id":this.friendid,
						"chat_type":1,
						"chat_content":this.content,
						"chat_time":this.chat_time
					},
					header: {
						'content-type': 'application/json',
						'chartset': 'utf-8'
					},
					success: res => {
						if(res.data == '1'){
							uni.hideLoading();
							// this.joinHistoryMsg();
							this.talkList.push({
								"user_id":this.userName,
								"friend_id":this.friendid,
								"chat_type":1,
								"chat_content":this.content,
								"chat_time":this.chat_time
							});
							// this.getHistoryMsg();
							this.$nextTick(()=>{
								// 清空内容框中的内容
								this.content = '';
								uni.pageScrollTo({
								    scrollTop: 999999,	// 设置一个超大值，以保证滚动条滚动到底部
								    duration: 0
								});
							})
						}else{
							console.log('报错！！！！！！！！！！！');
							uni.hideLoading();
						}
					}
				});
			}
		}
	}
</script>

<style lang="scss">
@import "./lib/global.scss";
	page{
		background-color: #F3F3F3;
		font-size: 28rpx;
	}
	.content{
		width: 100%;
	}
	/* 加载数据提示 */
	.tips{
		position: fixed;
		left: 0;
		top:var(--window-top);
		width: 100%;
		z-index: 9;
		background-color: rgba(0,0,0,0.15);
		height: 72rpx;
		line-height: 72rpx;
		transform:translateY(-80rpx);
		transition: transform 0.3s ease-in-out 0s;
		
		&.show{
			transform:translateY(0);
		}
	}
	
	.box-1{
		width: 100%;
		height: auto;
		padding-bottom: 100rpx;
		box-sizing: content-box;
		
		/* 兼容iPhoneX */
		margin-bottom: 0;  
		margin-bottom: constant(safe-area-inset-bottom);  
		margin-bottom: env(safe-area-inset-bottom);  
	}
	.box-2{
		position: fixed;
		left: 0;
		width: 100%;
		bottom: 0;
		height: auto;
		z-index: 2;
		border-top: #e5e5e5 solid 1px;
		box-sizing: content-box;
		background-color: #F3F3F3;
		
		/* 兼容iPhoneX */
		padding-bottom: 0;  
		padding-bottom: constant(safe-area-inset-bottom);  
		padding-bottom: env(safe-area-inset-bottom);  
		
		>view{
			padding: 0 20rpx;
			height: 100rpx;
		}
		
		.content{
			background-color: #fff;
			height: 64rpx;
			padding: 0 20rpx;
			border-radius: 32rpx;
			font-size: 28rpx;
		}
		
		.send{
			background-color: #0FAEFF;
			color: #fff;
			height: 64rpx;
			margin-left: 20rpx;
			border-radius: 32rpx;
			padding: 0;
			width: 120rpx;
			line-height: 62rpx;
			
			&:active{
				background-color: #5fc496;
			}
		}
	}
	
	.talk-list{
		padding-bottom: 20rpx;
		
		/* 消息项，基础类 */
		.item{
			padding: 20rpx 20rpx 0 20rpx;
			align-items:flex-start;
			align-content:flex-start;
			color: #333;
			
			.pic{
				width: 92rpx;
				height: 92rpx;
				border-radius: 50%;
				border: #fff solid 1px;
			}
			
			.content{
				padding: 20rpx;
				border-radius: 4px;
				max-width: 500rpx;
				word-break: break-all;
				line-height: 52rpx;
				position: relative;
			}
			
			/* 收到的消息 */
			&.pull{
				.content{
					margin-left: 32rpx;
					background-color: #fff;
					
					&::after{
						content: '';
						display: block;
						width: 0;
						height: 0;
						border-top: 16rpx solid transparent;
						border-bottom: 16rpx solid transparent;
						border-right: 20rpx solid #fff;
						position: absolute;
						top: 30rpx;
						left: -18rpx;
					}
				}
			}
			
			/* 发出的消息 */
			&.push{
				/* 主轴为水平方向，起点在右端。使不修改DOM结构，也能改变元素排列顺序 */
				flex-direction: row-reverse;
				
				.content{
					margin-right: 32rpx;
					background-color: #a0e959;
					
					&::after{
						content: '';
						display: block;
						width: 0;
						height: 0;
						border-top: 16rpx solid transparent;
						border-bottom: 16rpx solid transparent;
						border-left: 20rpx solid #a0e959;
						position: absolute;
						top: 30rpx;
						right: -18rpx;
					}
				}
			}
		}
	}
</style>