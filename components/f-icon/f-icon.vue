<template>
	<view class="iconfont f-icon"
		:class="['icon-' + name, 'font-' + size, 'f-icon-' + (color && color[0]) === '#' ? '' : color]"
		@click="clickHandler" :style="{color: (color && color[0]) === '#' ? color : ''}"></view>
</template>

<script>
	// #ifdef APP-NVUE
	// nvue通过weex的dom模块引入字体，相关文档地址如下：
	// https://weex.apache.org/zh/docs/modules/dom.html#addrule
	weex.requireModule('dom').addRule('fontFace', {
		'fontFamily': "icon-iconfont",
		'src': `url('https://at.alicdn.com/t/c/font_3655426_vtfll659mr.ttf')`
	})
	// #endif

	/**
	 * icon 图标
	 * @description 基于字体的图标集，包含了大多数常见场景的图标。
	 * @property {String}			name			图标名称，见示例图标集
	 * @property {String}			color			图标颜色,可接受主题色 （默认 color['u-content-color'] ）
	 * @property {String | Number}	size			图标字体大小，单位px （默认 '16px' ）
	 * @event {Function} click 点击图标时触发
	 * @event {Function} touchstart 事件触摸时触发
	 * @example <u-icon name="photo" color="#2979ff" size="28"></u-icon>
	 */
	export default {
		name: 'f-icon',
		// #ifdef VUE3
		emits: ['click'],
		// #endif
		props: {
			// 图标类名
			name: {
				type: String,
				default: ''
			},
			// 图标颜色，可接受主题色
			color: {
				type: String,
				default: 'black'
			},
			// 字体大小，单位px
			size: {
				type: [String, Number],
				default: 24
			},
		},
		data() {
			return {}
		},
		mixins: [uni.$f.mpMixin, uni.$f.mixin],
		computed: {
				test() {
					console.log('this.color', this.color[0]);
					return 123;
				}
		},
		methods: {
			clickHandler() {
				this.$emit('click')
			}
		}
	}
</script>

<style lang="scss" scoped>
	@import "f-icon.scss";
</style>
