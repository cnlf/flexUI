<template>
	<view :style="{ height: statusBarHeight + 'px'}" class="status-bar">
		<slot />
	</view>
</template>

<script>
	export default {
		name: 'f-status-bar',
		data() {
			return {
				statusBarHeight: 20
			}
		},
		mounted() {
			this.statusBarHeight = uni.getSystemInfoSync().statusBarHeight;
		}
	}
</script>

<style lang="scss" >
	.status-bar {
		height: 20px;
	}
</style>
