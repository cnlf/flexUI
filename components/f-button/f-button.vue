<template>
    <view class="f-btn" :class="[
		color ? 'f-btn-color-' + color : '',
		size ? 'f-btn-size-' + size : '',
		bg ? 'f-btn-bg-' + bg : '',
		(typeof round === 'boolean' && round) || round === 'true' ? 'round' : '',
		(typeof outline === 'boolean' && outline) || outline === 'true' ? 'f-btn-outline-' + color : '',
	]" @click="clickHandler">
		<text>{{label}}</text>
		<slot></slot>
	</view>
</template>

<script>
export default {
    name: "f-button",
    // #ifdef MP
    mixins: [uni.$f.mpMixin, uni.$f.mixin],
    // #endif
	emits: ['click'],
    data() {
        return {};
    },
    props: {
        disabled: {
            type: Boolean,
            default: false
        },
        loading: {
            type: Boolean,
            default: false
        },
        color: {
            type: String,
            default: 'primary'
        },
		bg: {
		    type: String,
		    default: ''
		},
        round: {
            type: [Boolean, String],
            default: false
        },
        outline: {
            type: [Boolean, String],
            default: false
        },
        label: {
            type: String,
            default: '按钮'
        },
		size: {
			type: String,
			default: ''
		}
    },
    computed: {
        // 生成bem风格的类名
    },
    methods: {
        clickHandler() {
            // 非禁止并且非加载中，才能点击
            if (!this.disabled && !this.loading) {
                // 进行节流控制，每this.throttle毫秒内，只在开始处执行
                uni.$f.throttle(() => {
                    this.$emit("click");
                }, 0);
            }
        },
        // 下面为对接uniapp官方按钮开放能力事件回调的对接
        getphonenumber(res) {
            this.$emit("getphonenumber", res);
        },
        getuserinfo(res) {
            this.$emit("getuserinfo", res);
        },
        error(res) {
            this.$emit("error", res);
        },
        opensetting(res) {
            this.$emit("opensetting", res);
        },
        launchapp(res) {
            this.$emit("launchapp", res);
        },
    },
};
</script>

<style lang="scss" scoped>
@import "f-button";
</style>
