<script>
export default {
	onLaunch: function() {
		console.log('App Launch');
		//调用API从本地缓存中获取数据
		// var users = wx.getStorageSync('users');
		// if (!users) {
		// 	users = this.globalData.loadUsers();
		// 	wx.setStorageSync('users', users);
		// }
		//调用API从本地缓存中获取数据
		// var songs = wx.getStorageSync('songs');
		// if (!songs) {
		// 	songs = this.globalData.loadSongs();
		// 	wx.setStorageSync('songs', songs);
		// }
	},
	globalData: {
		getUserInfo: function(cb) {
			var that = this;

			if (this.userInfo) {
				typeof cb == 'function' && cb(this.userInfo);
			} else {
				//调用登录接口
				wx.getUserInfo({
					withCredentials: false,
					success: function(res) {
						that.userInfo = res.userInfo;
						typeof cb == 'function' && cb(that.userInfo);
					}
				});
			}
		},
		// loadUsers: function() {
		// 	var users = new Array();
		// 	var user = new Object();
		// 	user.id = "0";
		// 	user.phone = "18010008888";
		// 	user.password = "123456";
		// 	users[0] = user;
		// 	var user1 = new Object();
		// 	user1.id = "0";
		// 	user1.phone = "13710008888";
		// 	user1.password = "123456";
		// 	users[1] = user1;
		// 	var user2 = new Object();
		// 	user2.id = "0";
		// 	user2.phone = "13310003333";
		// 	user2.password = "123456";
		// 	users[2] = user2;
		// 	return users;
		// },
		// loadSongs: function() {
		// 	var songs = new Array();
		// 	var song = new Object();
		// 	song.id = 1;
		// 	song.name = '微微一笑很倾城';
		// 	song.singer = '杨洋';
		// 	song.img = 'http://y.gtimg.cn/music/photo_new/T002R90x90M000003RxTdZ0sJLwo.jpg';
		// 	song.url = 'http://stream.qqmusic.tc.qq.com/137903929.mp3';
		// 	song.type = '2';
		// 	song.courseid = 137903929;
		// 	songs.push(song);
		// 	var song2 = new Object();
		// 	song2.id = 2;
		// 	song2.name = '你还要我怎样';
		// 	song2.singer = '薛之谦';
		// 	song2.img = 'http://y.gtimg.cn/music/photo_new/T002R90x90M000000aWdOx24i3dG.jpg';
		// 	song2.url = 'http://stream.qqmusic.tc.qq.com/138549169.mp3';
		// 	song2.type = '1';
		// 	song2.courseid = 138549169;
		// 	songs.push(song2);
		// 	var song3 = new Object();
		// 	song3.id = 3;
		// 	song3.name = '演员';
		// 	song3.singer = '薛之谦';
		// 	song3.img = 'http://y.gtimg.cn/music/photo_new/T002R90x90M000003y8dsH2wBHlo.jpg';
		// 	song3.url = 'http://stream.qqmusic.tc.qq.com/132636799.mp3';
		// 	song3.type = '1';
		// 	song3.courseid = 132636799;
		// 	songs.push(song3);
		// 	var song4 = new Object();
		// 	song4.id = 4;
		// 	song4.name = '此时此刻';
		// 	song4.singer = '许巍';
		// 	song4.img = 'http://y.gtimg.cn/music/photo_new/T002R300x300M000003rsKF44GyaSk.jpg?max_age=2592000';
		// 	song4.url = 'http://stream.qqmusic.tc.qq.com/132636799.mp3';
		// 	song4.type = '1';
		// 	song4.courseid = 2592000;
		// 	songs.push(song4);
		// 	var song5 = new Object();
		// 	song5.id = 5;
		// 	song5.name = '告白气球';
		// 	song5.singer = '周杰伦';
		// 	song5.img = 'http://y.gtimg.cn/music/photo_new/T002R90x90M000003RMaRI1iFoYd.jpg';
		// 	song5.url = 'http://stream.qqmusic.tc.qq.com/137192078.mp3';
		// 	song5.type = '1';
		// 	song5.courseid = 137192078;
		// 	songs.push(song5);
		// 	return songs;
		// },
		userInfo: null
	},
	onShow: function() {
		console.log('App Show');
	},
	onHide: function() {
		console.log('App Hide');
	}
};
</script>

<style>
/* 头条小程序需要把 iconfont 样式放到组件外 */
@import 'components/m-icon/m-icon.css';

/*每个页面公共css */
page {
	min-height: 100%;
	display: flex;
	font-size: 16px;
}

/* #ifdef MP-BAIDU */
page {
	width: 100%;
	height: 100%;
	display: block;
}

swan-template {
	width: 100%;
	min-height: 100%;
	display: flex;
}

/* 原生组件模式下需要注意组件外部样式 */
custom-component {
	width: 100%;
	min-height: 100%;
	display: flex;
}

/* #endif */

/* #ifdef MP-ALIPAY */
page {
	min-height: 100vh;
}

/* #endif */

/* 原生组件模式下需要注意组件外部样式 */
m-input {
	width: 100%;
	/* min-height: 100%; */
	display: flex;
	flex: 1;
}

.content {
	display: flex;
	flex: 1;
	flex-direction: column;
	background-color: #efeff4;
	padding: 10px;
}

.input-group {
	background-color: #ffffff;
	margin-top: 20px;
	position: relative;
}

.input-group::before {
	position: absolute;
	right: 0;
	top: 0;
	left: 0;
	height: 1px;
	content: '';
	-webkit-transform: scaleY(0.5);
	transform: scaleY(0.5);
	background-color: #c8c7cc;
}

.input-group::after {
	position: absolute;
	right: 0;
	bottom: 0;
	left: 0;
	height: 1px;
	content: '';
	-webkit-transform: scaleY(0.5);
	transform: scaleY(0.5);
	background-color: #c8c7cc;
}

.input-row {
	display: flex;
	flex-direction: row;
	position: relative;
	font-size: 18px;
	line-height: 40px;
}

.input-row .title {
	width: 72px;
	padding-left: 15px;
}

.input-row.border::after {
	position: absolute;
	right: 0;
	bottom: 0;
	left: 8px;
	height: 1px;
	content: '';
	-webkit-transform: scaleY(0.5);
	transform: scaleY(0.5);
	background-color: #c8c7cc;
}

.btn-row {
	margin-top: 5px;
	/* padding: 2px; */
}

button.primary {
	background-color: #0faeff;
}

/* uni.css - 通用组件、模板样式库，可以当作一套ui库应用 */
@import '/common/uni.css';

.container {
	font-size: $font-base;
	overflow: hidden;
	width: 100%;
}

.wrapper {
	font-size: $font-base;
	text-align: left;
}

* {
	transition: width 2s;
	-moz-transition: width 2s;
	-webkit-transition: width 2s;
	-o-transition: width 2s;
}

/* font color */
* {
	color: #353535;
}

.gray {
	color: #808080;
}

.wxParse {
	text-indent: 2em;
}

.wxParse image {
	text-indent: 0;
}

.wxParse ._view {
	padding: 0 10rpx;
	text-indent: 0;
}

.wxParse .p {
	text-indent: 1em;
}
/* 张昱 */
.hr {
	height: 1px;
	background-color: #cccccc;
	opacity: 0.3;
}
</style>
