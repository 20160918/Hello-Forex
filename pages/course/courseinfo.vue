<!-- <template>
	<view class="imt-audio" style="text-align: center;margin-top: 200upx;width: 100%;font-size: 20px;">
		<view class="audio-wrapper">
			<view class="audio-number">{{currentTime}}</view>
			<slider class="audio-slider" :activeColor="color" block-size="16" :value="current" :max="duration" @changing="seek=true,current=$event.detail.value"
			 @change="change"></slider>
			<view class="audio-number">{{durationTime}}</view>
		</view>
		<view class="audio-control-wrapper" :style="{color:color}">
			<view class="audio-control audio-control-prev" v-if="control" :style="{borderColor:color}" @click="prev">&#xe601;</view>
			<view class="audio-control audio-control-switch" :class="{audioLoading:loading}" :style="{borderColor:color}" @click="operation">{{loading?'&#xe600;':(paused?'&#xe865;':'&#xe612;')}}</view>
			<view class="audio-control audio-control-next" v-if="control" :style="{borderColor:color}" @click="next">&#xe601;</view>
		</view>
	</view>
</template>

<script>
	const audio = uni.createInnerAudioContext(); //创建音频
	export default {
		data() {
			return {
				currentTime: '', //当前播放时间
				durationTime: '', //总时长
				current: '', //slider当前进度
				loading: false, //是否处于读取状态
				paused: true, //是否处于暂停状态
				seek: false //是否处于拖动状态
			}
		},
		props: {
			src: String, //音频链接
			autoplay: Boolean, //是否自动播放
			duration: Number, //总时长（单位：s）
			control: {
				type:Boolean,
				default:true
			}, //是否需要上一曲/下一曲按钮
			continue:Boolean,//播放完成后是否继续播放下一首，需定义@next事件
			color: {
				type:String,
				default:'#169af3'
			} //主色调
		},
		methods: {
			//返回prev事件
			prev() {
				this.$emit('prev')
			},
			//返回next事件
			next() {
				this.$emit('next')
			},
			//格式化时长
			format(num) {
				return '0'.repeat(2 - String(Math.floor(num / 60)).length) + Math.floor(num / 60) + ':' + '0'.repeat(2 - String(
					Math.floor(num % 60)).length) + Math.floor(num % 60)
			},
			//播放/暂停操作
			operation() {
				if (audio.paused) {
					audio.play()
					this.loading = true
				} else {
					audio.pause()
				}
			},
			//完成拖动事件
			change(e) {
				audio.seek(e.detail.value)
			}
		},
		created() {
			audio.src = this.src
			this.current = 0
			this.durationTime = this.format(this.duration)
			audio.obeyMuteSwitch = false
			audio.autoplay = this.autoplay
			//音频进度更新事件
			audio.onTimeUpdate(() => {
				if (!this.seek) {
					this.current = audio.currentTime
				}
			})
			//音频播放事件
			audio.onPlay(() => {
				this.paused = false
				this.loading = false
			})
			//音频暂停事件
			audio.onPause(() => {
				this.paused = true
			})
			//音频结束事件
			audio.onEnded(() => {
				if (this.continue) {
					this.next()
				} else {
					this.paused = true
					this.current = 0
				}
			})
			//音频完成更改进度事件
			audio.onSeeked(() => {
				this.seek = false
			})
		},
		watch: {
			//监听音频地址更改
			src(e) {
				audio.src = e
				this.current = 0
				audio.play()
				this.loading = true
			},
			//监听总时长改变
			duration(e) {
				this.durationTime = this.format(e)
			},
			//监听当前进度改变
			current(e) {
				this.currentTime = this.format(e)
			}
		}
	}
</script>

<style>
	@font-face {
		font-family: 'icon';
		src: url('//at.alicdn.com/t/font_1104838_fxzimc34xw.eot');
		src: url('//at.alicdn.com/t/font_1104838_fxzimc34xw.eot?#iefix') format('embedded-opentype'),
			url('//at.alicdn.com/t/font_1104838_fxzimc34xw.woff2') format('woff2'),
			url('//at.alicdn.com/t/font_1104838_fxzimc34xw.woff') format('woff'),
			url('//at.alicdn.com/t/font_1104838_fxzimc34xw.ttf') format('truetype'),
			url('//at.alicdn.com/t/font_1104838_fxzimc34xw.svg#iconfont') format('svg');
	}

	.imt-audio {
		padding: 30upx;
		background: #fff;
		border-radius: 20upx;
		border: #007AFF 1upx solid;
		width: 100%;
		height: 20%;
		margin: 10upx;
	}

	.audio-wrapper {
		display: flex;
		align-items: center;
	}

	.audio-number {
		font-size: 24upx;
		line-height: 1;
		color: #333;
	}

	.audio-slider {
		flex: 1;
		margin: 0 30upx;
	}

	.audio-control-wrapper {
		margin-top: 20upx;
		display: flex;
		justify-content: center;
		align-items: center;
		font-family: "icon" !important;
	}

	.audio-control {
		font-size: 32upx;
		line-height: 1;
		border: 4upx solid;
		border-radius: 50%;
		padding: 16upx;
	}

	.audio-control-next {
		transform: rotate(180deg);
	}

	.audio-control-switch {
		font-size: 40upx;
		margin: 0 80upx;
	}

	.audioLoading {
		animation: loading 2s;
		animation-iteration-count: infinite;
		animation-timing-function: linear;
	}

	@keyframes loading {
		to {
			transform: rotate(360deg);
		}
	}
</style> -->
<template>
	<view class="content">
		<imt-audio continue :src="audio[now].src" :duration="audio[now].duration" @prev="now = now === 0?audio.length-1:now-1"
		 @next="now = now === audio.length-1?0:now+1"></imt-audio>
		<!-- 
			 src: String 音频链接*必须*
			 duration: Number 总时长（单位：s）*必须*
			 autoplay: Boolean 是否自动播放*默认false*
			 control: Boolean 是否需要上一曲/下一曲按钮*默认true*
			 continue:Boolean 播放完成后是否继续播放下一首，需定义@next事件*默认false*
			 color: String 主色调*默认#169af3*
			 @prev:点击上一首按钮
			 @next:点击下一首按钮
		  -->
		<view class="list" :class="{active:key===now}" v-for="(item,key) in audio" :key="key" @click="now = key">
			<view class="list-title">第{{key+1}}课</view>
			<image class="list-pic" src="http://mouyizhan.com/music.gif"></image>
		</view>
	</view>
</template>

<script>
	import imtAudio from 'components/imt-audio/imt-audio'
	export default {
		data() {
			return {
				audio: [{
						src: 'http://mouyizhan.com/1.mp3',
						duration: 212
					},
					{
						src: 'http://mouyizhan.com/2.mp3',
						duration: 189
					},
					{
						src: 'http://mouyizhan.com/3.mp3',
						duration: 214
					},
					{
						src: 'http://mouyizhan.com/4.mp3',
						duration: 205
					},
					{
						src: 'http://mouyizhan.com/5.mp3',
						duration: 228
					}
				],
				now: 0
			}
		},
		components: {
			imtAudio
		}
	}
</script>

<style>
	page{
		background: #f5f5f5;
	}
	.content {
		padding: 20upx;
	}

	.list {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 0 30upx;
		background: #fff;
		border-radius: 10upx;
		margin-top: 20upx;
		color: #333;
	}

	.list-title {
		font-size: 28upx;
		line-height: 88upx;
	}

	.list-pic {
		display: none;
		width: 28upx;
		height: 28upx;
	}

	.active {
		background: #169af3;
		color: #fff;
	}

	.active .list-pic {
		display: block;
	}
</style>