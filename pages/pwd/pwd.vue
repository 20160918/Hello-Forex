<template>
	<view class="forget" style="width: 99%;">
		<view class="content">
			<!-- 主体 -->
			<view class="main">
				<view class="tips">若你忘记了密码，可在此重置新密码。</view>
				<wInput v-model="phoneData" type="text" maxlength="11" placeholder="手机号" @input="bindPhoneInput"></wInput>
				<wInput v-model="passData" type="password" maxlength="11" placeholder="登录密码" isShowPass @input="bindPasswordInput"></wInput>
				<view style="width: 100%;display: flex;flex-direction: row;">
				   <wInput v-model="verCode" type="number" maxlength="4" placeholder="验证码" @input="bindCodeInput" style="width:65% !important;"></wInput>
				   <button  @tap="getVerCode" :hidden="hidden" :disabled="btnDisabled" style="width: 35%;height:100upx;line-height: 100upx;border-radius:2.5rem;text-align: center;margin-top: 24upx;font-size: 28upx;">{{btnValue}}</button>
				</view>
			</view>

			<wButton text="重置密码" :disabled="disabled" @tap="startRePass"></wButton>
		</view>
	</view>
</template>

<script>
var _this;
var zhenzisms = require("../../utils/zhenzisms.js"); //获取应用实例
import service from '../../service.js';
import mInput from '../../components/m-input.vue';
import wInput from '../../components/watch-login/watch-input.vue'; //input
import wButton from '../../components/watch-login/watch-button.vue'; //button

export default {
	data() {
		return {
			phoneData: '', //电话
			passData: '', //密码
			verCode: '', //验证码

			hidden: true,
			btnValue: '',
			btnDisabled: false,
			second: 60,
			disabled: false,
		};
	},
	components: {
		wInput,
		wButton
	},
	mounted() {
		_this = this;
	},
	methods: {
		//密码输入
		bindPasswordInput(e) {
			this.passData = e;
		},
		
		//手机号输入
		bindPhoneInput(e) {
			this.phoneData = e;
			uni.request({
				url: 'http://127.0.0.1:5000/registered',
				method: 'POST',
				data: {
					"stu_account":this.phoneData
				},
				header: {
					'content-type': 'application/json',
					'chartset': 'utf-8'
				},
				success: res => {
					// console.log(res.data);
					if(res.data == "1"){
						this.hidden = false,
						this.btnValue = '获取验证码'
					}
					else{
						this.hidden = true
					}
				}
			});	
		},
		//验证码输入
		bindCodeInput(e) {
			this.verCode = e
		},
		//获取验证码
		getVerCode(e) {
			var that = this;
			zhenzisms.client.init('https://sms_developer.zhenzikj.com', '104780', 'ef94e2c0-9dcd-488f-83fd-7c0d8157ba9d');
			var params = {};
			params.number = that.phoneData;
			params.message = '您的验证码为:{code},请不要把验证码泄露给其他人!5分钟内有效.';
			params.seconds = 60 * 5;
			params.length = 4; 
			zhenzisms.client.sendCode(function (res) {
			  wx.showToast({
			    title: res.data.data,
			    icon: 'none',
			    duration: 2000
			  });
			}, params);
			this.timer();
		},
		timer: function () {
		  let promise = new Promise((resolve, reject) => {
		    let setTimer = setInterval(() => {
		      var second = this.second - 1;
			  this.second = second;
			  this.btnValue = second + '秒';
			  this.btnDisabled = true;
			  if (this.second <= 0) {
				  this.second = 60,
				  this.btnValue = '获取验证码',
				  this.btnDisabled = false,
				  resolve(setTimer)
			  	}
		    }, 1000);
		  });
		  promise.then(setTimer => {
		    clearInterval(setTimer);
		  });
		},
		showToast(msg) {
		  wx.showToast({
		    title: msg,
		    icon: 'none',
		    duration: 2000
		  });
		},
		//重置
		startRePass(e) {
		  var that = this; //检验验证码
		  var result = zhenzisms.client.validateCode(this.phoneData, this.verCode);
		  if (result == 'ok') { // 注册用户存入缓存
			uni.request({
				url: 'http://127.0.0.1:5000/updatePass',
				method: 'POST',
				data: {
					"stu_account":this.phoneData,
					"stu_password":this.passData
				},
				header: {
					'content-type': 'application/json',
					'chartset': 'utf-8'
				},
				success: res => {
					// console.log(res.data);
					if(res.data == "1"){
						uni.showToast({
							title: '重置密码成功！',
							icon: 'none',
							duration: 1000
						});
						uni.redirectTo({
						    url: '../login/login?register=true'
						});
					}
				}
			});			
		    // wx.redirectTo({
		    //   url: '../login/login'
		    // });
		  } else if (result == 'empty') {
		    that.showToast('验证错误, 未生成验证码!');
		  } else if (result == 'number_error') {
		    that.showToast('验证错误，手机号不一致!');
		  } else if (result == 'code_error') {
		    that.showToast('验证错误，验证码不一致!');
		  } else if (result == 'code_expired') {
		    that.showToast('验证错误，验证码已过期!');
		  }
		}
	}
};
</script>

<style>
@import url('../../components/watch-login/css/icon.css');
@import url('./css/main.css');
</style>
