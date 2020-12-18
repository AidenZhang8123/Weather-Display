<script>
	export default {
		
		onLaunch: function() {
			console.log('App Launch');
			// plus.navigator.setFullscreen(true);//隐藏状态栏
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	/* uni.css - 通用组件、模板样式库，可以当作一套ui库应用 */
	@import './common/uni.css';
</style>
