<template>
	<view class="page_edu">
		<view class="page_content">
			<view class="container">
				<view class="uni-form-item ">
					<view class="title">姓名:</view>
					<input class="uni-input" @input="onKeyInput" placeholder="姓名" ></input>
				</view>
				<view class="hr"></view>
				<view class="uni-form-item ">
					<view class="title" style="margin-top: 5px;">性别：</view>
					<view class="sexlist">
						<radio-group @change="radioChange1">
							<label class="sextype" v-for="(item, index) in sexs" :key="item.value">
								<view><radio :value="item.value" :checked="index === current" /></view>
								<view>{{ item.name }}</view>
							</label>
						</radio-group>
					</view>
				</view>
				<view class="hr"></view>
				<view class="uni-form-item ">
					<view class="title">年龄：</view>
					<input class="uni-input" type="number" @input="onKeyInput1" placeholder="年龄" ></input>
				</view>
				<view class="hr"></view>
				<view class="uni-form-item ">
					<view class="title">注册手机号：</view>
					<input class="uni-input" type="number" maxlength="11" @input="onKeyInput2" placeholder="注册手机号"></input>
				</view>
				<view class="hr"></view>
				<view class="uni-form-item ">
					<view class="title">您所在的城市：</view>
					<input class="uni-input" @input="onKeyInput3" placeholder="所在城市" ></input>
				</view>
				<view class="hr"></view>
			</view>
		</view>
		<view class="page_edu_header">
			<view class="uni-title">
				<text>下面选项中，您对哪个方向的知识最感兴趣？</text>
			</view>
			<view class="uni-list">
				<radio-group @change="radioChange">
					<label class="uni-list-cell" v-for="(item, index) in items" :key="item.value">
						<view><radio :value="item.value" :checked="index === current" /></view>
						<view>{{ item.name }}</view>
					</label>
				</radio-group>
			</view>
		</view>
		<!-- <view class="hr"></view> -->
		<view class="page_footer">
			<button @tap="submit">提交</button>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			name:'',
			sex: '',
			age: 0,
			phone: '',
			position: '',
			interest: '',
			// current: 0,
			// current1: 0,
			items: [
				{
					value: '形象气质管理',
					name: '形象气质管理'
				},
				{
					value: '家庭沟通',
					name: '家庭沟通'
				},
				{
					value: '演说家',
					name: '演说家'
				},
				{
					value: '传统国学礼仪',
					name: '传统国学礼仪'
				},
				{
					value: '女性总裁',
					name: '女性总裁'
				},
				{
					value: '亲子研学营',
					name: '亲子研学营'
				}
			],
			sexs: [
				{
					value: '男',
					name: '男'
				},
				{
					value: '女',
					name: '女'
				}
			],
		};
	},
	methods: {
		onKeyInput: function(event) {
			this.name = event.target.value
		 },
		 onKeyInput1: function(event) {
			 this.age = event.target.value
		 },
		 onKeyInput2: function(event) {
			 this.phone = event.target.value
		 },
		 onKeyInput3: function(event) {
			 this.position = event.target.value
		 },
		radioChange: function(evt) {
			for (let i = 0; i < this.items.length; i++) {
				if (this.items[i].value === evt.target.value) {
					this.interest = evt.target.value;
					break;
				}
			}
		},
		radioChange1: function(evt) {
			for (let i = 0; i < this.sexs.length; i++) {
				if (this.sexs[i].value === evt.target.value) {
					this.sex = evt.target.value;
					break;
				}
			}
		},
		submit(){
			uni.request({
				url: 'http://127.0.0.1:5000/updateStudent',
				method: 'POST',
				data: {
					"stu_account": this.phone,
					"stu_name": this.name,
					"stu_sex": this.sex,
					"stu_age": this.age,
					"stu_phone": this.phone,
					"stu_position": this.position,
					"stu_interest": this.interest
				},
				header: {
					'content-type': 'application/json',
					'chartset': 'utf-8'
				},
				success: res => {
					console.log(res.data);
					if(res.data == "1"){
						uni.showToast({
							title: '提交成功',
							icon: 'none',
							duration: 1000
						});
						uni.redirectTo({
						    url: '../login/login?register=true'
						});
					}else{
						uni.showToast({
							title: '提交失败',
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

<style lang="scss" scoped>
@function realSize($args) {
	@return $args / 1.5;
}

.page_edu {
	width: 100%;
	padding: 0 10px;
}

.page_edu_header {
	width: 100%;
	text-align: left;
}
.uni-list{
	margin-top: 10px;
	margin-left: 20px;
}
.uni-list-cell{
	display: flex;
	flex-direction: row;
	margin-bottom: 10px;
}
.uni-form-item {
	display: flex;
	flex-direction: row;
	padding: 5px;
}
.sexlist radio-group{
	display: flex;
	flex-direction: row;
	padding: 5px;
}
.sextype{
	display: flex;
	flex-direction: row;
	margin-right: 20px;
}

.page_content {
	width: 100%;
	text-align: center;
}
.page_footer{
	margin-top: 5px;
}

</style>
