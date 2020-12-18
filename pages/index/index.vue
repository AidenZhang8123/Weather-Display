<template>
	<view>
		<image src="../../static/bg.png" class="the-back-img"></image>
		<view class="uni-padding-wrap uni-common-mt mainbox" v-on:touchend="showSixDays()" :class="{ changBottom: toChangBottom }">
			<view class="uni-flex uni-row first-box">
				<!-- 今天 -->
				<view class="flex-item" style="-webkit-flex: 1;flex: 1;">
					<view class="nowtempreature">{{ todayWeather.tem }}°</view>
				</view>
				<!-- 明天 -->
				<view class="flex-item" style="-webkit-flex: 1;flex: 1;">
					<view class="uni-flex uni-row">
						<view class="flex-item todaybox" style="-webkit-flex: 1;flex: 1;"><image :src="theImg" class="wehterimg"></image></view>
						<view class="flex-item tomrrowboxSmall" style="-webkit-flex: 1;flex: 1;">
							<view class="tomrrowtempeature">{{ tomorrow.tem1 }}</view>
							<view class="tomrrowtempeature">{{ tomorrow.tem2 }}</view>
						</view>
					</view>
				</view>
			</view>
			<view class="uni-flex uni-row second-box">
				<!-- 今天 -->
				<view class="flex-item" style="-webkit-flex: 1;flex: 1;">
					<view>今日 {{ todayWeather.week }} {{ todayWeather.wea }} </view>
					<view>最高{{ todayWeather.tem1 }}度 最低 {{ todayWeather.tem2 }}度</view>
					<view>{{ todayWeather.win }} {{ todayWeather.win_speed }}</view>
					<view>空气质量 {{ todayWeather.air_level }} {{ todayWeather.air_pm25 }}</view>
				</view>
				<!-- 明天 -->
				<view class="flex-item" style="-webkit-flex: 1;flex: 1;">
					<view>明日 {{ tomorrow.week }} {{ tomorrow.wea }} </view>
					<!-- <view>最高{{ tomorrow.tem1 }}度 最低 {{ tomorrow.tem2 }}度</view> -->
					<view>{{ tomorrow.win[0] }}转{{ tomorrow.win[1] }} {{ tomorrow.win_speed }}</view>
					<view>{{ tomorrow.index[3].title }} {{ tomorrow.index[3].level }}</view>
					<view>{{ tomorrow.index[5].title }} {{ tomorrow.index[5].level }}</view>
				</view>
			</view>
			<view class="uni-flex uni-row third-box">
				<view class="flex-item six-days-weather" v-for="weather of sevenDays">
					<image :src="require(`static/${weather.wea_img}.svg`)"></image>
					<view>{{ weather.wea }}</view>
					<view>{{ weather.week }}</view>
					<view>{{ weather.date }}</view>
					<view>
						<span>{{ weather.tem1 }}</span>
						<span>{{ weather.tem2 }}</span>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			sixDaysStatus: 1,
			sixDaysTimer: '',
			toChangBottom: false,
			todayWeather: {},
			sevenDays: [],
			tomorrow: {},
			theImg: {},
			screenBright:'',
			 date: new Date(),
			 h:''
		};
	},
	onLoad() {
		// 保持屏幕常亮
		uni.setKeepScreenOn({
			keepScreenOn: true
		});
		
		this.getToday();
		this.getSevenDays();
	},
	methods: {
		showSixDays: function() {
			if ((this.sixDaysStatus = 1)) {
				this.sixDaysStatus = 2;
				clearTimeout(this.sixDaysTimer);
				this.sixDaysTimer = setTimeout(this.hideSixDays, 1000 * 10);
				this.toChangBottom = true;
			} else {
				this.sixDaysStatus = 3;
			}
		},
		hideSixDays: function() {
			this.sixDaysStatus = 1;
			this.toChangBottom = false;
		},

		getToday() {
			uni.request({
				url: 'https://tianqiapi.com/api', //免费接口，如果用V6版本号取不到数据，用专业版 的v61 可以。
				// url:'https://v0.yiketianqi.com/api', //专业实况天气接口 免费用户也可取到数据 2000次
				data: {
					appid: '98711858',
					appsecret: '2XIPPAUj',
					version: 'v6', //用专业版 的v61 可取到数据
					city: '北京'
				},
				method: 'GET',
				header: {
					'Content-Type': 'application/x-www-form-urlencoded'
				},
				success: res => {
					let callback = res.statusCode;
					if ((callback = 200)) {
						this.todayWeather = res.data;
						console.log('今天' + res.data);
					} else {
						console.log('网络错误' + this.callback);
					}
				}
			});
		},

		getSevenDays() {
			uni.request({
				// url: 'https://v0.yiketianqi.com/api', //专业接口，免费用户也可取到数据 2000次
				url: 'https://tianqiapi.com/api', //免费接口 24小时 200次
				data: {
					appid: '98711858',
					appsecret: '2XIPPAUj',
					version: 'v1',
					city: '北京'
				},
				method: 'GET',
				header: {
					'Content-Type': 'application/x-www-form-urlencoded'
				},
				success: res => {
					let callback = res.statusCode;
					if ((callback = 200)) {
						this.sevenDays = res.data.data;
						this.tomorrow = res.data.data[1];
						let tomorrowImg = res.data.data[1].wea_img;
						this.theImg = `../../static/${tomorrowImg}.svg`;
						this.winds = res.data.data[1].win;
						console.log('明天' + this.tomorrow.day);
					} else {
						console.log('网络错误' + this.callback);
					}
				}
			});
		}
	},
	filters: {
	    formatDateTime(value) {
	      let date = new Date(value);
	      // let h = date.getHours();
	      // h = h < 10 ? "0" + h : h;
	      // return  h 
	    }
	  },
	beforeDestroy() {
	    if (this.timer) {
	      clearInterval(this.timer); //在Vue实例销毁前，清除定时器
	    }
	  },
	mounted() {
		var that = this
		this.screenBright = 0
		    this.timer = setInterval(() => {
				this.getToday();
				this.getSevenDays();
				
				let theHour = that.date.getHours();
				if( theHour < 22 || theHour > 6){
				this.screenBright = 0.7 // 屏幕亮度最亮1 最暗0
				// 改变屏幕亮度
				uni.setScreenBrightness({
				    value: this.screenBright,				
				});
				}
			  // console.log('time:' + that.date.getHours()+ '屏幕亮度' + this.screenBright)
		    }, 1000*60*30);
		
	}
};
</script>

<style lang="scss">
.the-back-img {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
}
.mainbox {
	color: #ffffff;
	font-family: Arial, Helvetica, sans-serif;
	position: absolute;
	bottom: 0;
	height: 400rpx;
	// background-color: #2c405a;
}
.changBottom {
	bottom: 200rpx;
}
.first-box {
	padding-left: 20rpx;
	.wehterimg {
		width: 150rpx;
		height: 150rpx;
	}
	.nowtempreature {
		font-size: 160rpx;
		display: inline-block;
		line-height: 160rpx;
	}
	.tomrrowtempeature {
		font-size: 46px;
		text-align: right;
	}
	.tomrrowboxSmall {
		padding-right: 40rpx;
	}
}
.second-box {
	padding-left: 20rpx;
}
.third-box {
	padding-left: 20rpx;
	margin-top: 50rpx;
	// background-color: #007AFF;
	.six-days-weather {
		-webkit-flex: 1;
		flex: 1;
		text-align: center;
		image {
			width: 80rpx;
			height: 80rpx;
		}
		view {
			font-size: 14rpx;
			line-height: 1.5;
			span {
				font-size: 18rpx;
				display: inline-block;
				width: 50%;
				text-align: center;
			}
		}
	}
}
</style>
