<template>
	<view class="row flex-center wrap f-tab">
        <view class="align-items-center q-mx-10" v-for="(item, index) of list" :key="index"
              :class="{'active' : index === tabIndex}" @click="changeTab(index)">
            <view class="font-14">{{item.name}}</view>
            <view class="line" :class="['line-' + lineColor]" :style="{width: lineWidth + '%'}"></view>
        </view>
	</view>
</template>

<script>
	export default {
		name: 'f-tab',
		mixins: [uni.$f.mpMixin, uni.$f.mixin],
        emits: ['update:tabIndex'],
		data() {
			return {

			}
		},
		props: {
		    list: {
		        type: Array,
		        default: []
		    },
            lineColor: {
		        type: String,
		        default: 'primary'
		    },
            lineWidth: {
		        type: Number,
		        default: 50
		    },
            tabIndex: {
		        type: Number,
		        default: 0
		    },
		},
		methods: {
            changeTab(i) {
                this.$emit('update:tabIndex', i);
            }
		},
	}
</script>

<style lang="scss" scoped>
@import "f-tab";
</style>
