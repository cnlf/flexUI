<template>
    <view class="popup" :class="(options && options.position) || 'center'">
        <view class="q-pa-15 popup-container bg-white" :class="customClass">
            <view class="row justify-content-between align-items-center">
                <view class="placeholder"></view>
                <text class="font-16 font-bold" v-show="options && options.title">{{ options.title }}</text>
                <f-icon v-show="options && options.close" name="close" @click="close()" />
            </view>
            <view class="q-py-15 body">
                <slot></slot>
            </view>
            <f-button v-if="options && options.btn" :color="options && options.btnColor ? options.btnColor : 'primary'" @tap="ok()" :label="options.btn || '好的'"></f-button>
        </view>
    </view>
</template>

<script>
	export default {
		name: 'f-popup',
		emits: ['close', 'ok'],
	    props: {
            options: {
                type: Object,
                default: () => {}
            },
			 customClass: {
				 type: String,
				 default: ''
			 },
        },
        methods: {
            close() {
                this.$emit('close');
            },
            ok() {
                this.$emit('ok');
            }
        }
	}
</script>

<style lang="scss" scoped>
@import './f-popup';
</style>
