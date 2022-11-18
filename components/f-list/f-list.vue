<template>
    <!-- #ifdef APP-NVUE -->
    <list ref="list" class="f-list" :enable-back-to-top="enableBackToTop" loadmoreoffset="15" :scroll-y="scrollY"
        @loadmore="loadMore">
        <slot />
    </list>
    <!-- #endif -->
    <!-- #ifdef H5 || MP-WEIXIN || MP-QQ -->
    <scroll-view class="f-list" :scroll-top="scrollTop"  :enable-flex="enableFlex" :enable-back-to-top="enableBackToTop" :scroll-y="scrollY"
                 :refresher-enabled="enableRefresh" :refresher-triggered="triggered" :lower-threshold="lowerThreshold"
                 :refresher-background="refresherBackground"
                 @scroll="scroll" @refresherpulling="onPulling" @refresherrefresh="onRefresh"
                 @refresherrestore="onRestore" @scrolltolower="onScrollToLower">
        <slot />
    </scroll-view>
    <!-- #endif -->
    <!-- #ifdef MP-ALIPAY || MP-BAIDU || MP-TOUTIAO -->
    <scroll-view class="f-list" :scroll-y="scrollY" @@scrorefresherrefreshlltolower="loadMore">
        <slot />
    </scroll-view>
    <!-- #endif -->
</template>

<script>
    export default {
        name: 'f-list',
        emit: ['scrollToLower', 'refresh'],
		options: {
			// #ifdef MP-WEIXIN
			multipleSlots: false,
			// #endif
		},
        data() {
            return {
                // 设置当前下拉刷新状态，true 表示下拉刷新已经被触发，false 表示下拉刷新未被触发
                triggered: false,
                // 刷新中
                refreshing: false,
                scrollTop: 0,
                old: {
                    scrollTop: 0
                }
			}
        },
        props: {
            // iOS点击顶部状态栏、安卓双击标题栏时，滚动条返回顶部，只支持竖向
            enableBackToTop: {
                type: Boolean,
                default: false
            },
            // 允许纵向滚动
            scrollY: {
                type: Boolean,
                default: true
            },
            // 启用 flexbox 布局。开启后，当前节点声明了 display: flex 就会成为 flex container，并作用于其孩子节点。
            enableFlex: {
                type: Boolean,
                default: true
            },
            // 开启自定义下拉刷新
            enableRefresh: {
                type: Boolean,
                default: true
            },
            // 开启自定义下拉刷新
            lowerThreshold: {
                type: Number,
                default: 80
            },
            // 设置自定义下拉刷新区域背景颜色
            refresherBackground: {
                type: String,
                default: '#fff'
            }
        },
        created() {
            // #ifndef APP-NVUE
            this.pullDown = {
                threshold: 95,
                maxHeight: 200,
                callRefresh: 'onrefresh',
                callPullingDown: 'onpullingdown',
                refreshSelector: '.c-refresh'
            };
            this.height = 0;
            this.canPullDown = false;
            this.refreshInstance = {};
            // #endif
        },
        methods: {
			// 滚动时触发
            scroll(e){
                this.old.scrollTop = e.detail.scrollTop;
            },
            // 自定义下拉刷新控件被下拉
            onPulling() {},
            // 自定义下拉刷新被触发
            onRefresh() {
                if (this.refreshing) return;
                this.refreshing = true;
                this.$emit("refresh");
            },
            // 自定义下拉刷新被复位
            onRestore() {
                // 需要重置
                this.triggered = 'restore';
			},
            // 滚动到底部/右边，会触发 scrollToLower 事件
            onScrollToLower() {
                this.$emit("scrollToLower");
            },
            // 停止下拉事件
            stop() {
                this.scrollTop = this.old.scrollTop;
                this.$nextTick(function() {
                    this.scrollTop = 0;
                    this.triggered = 'restore';
                    setTimeout(() => {
                        this.triggered = false;
                        this.refreshing = false;
                    })
                });
            },

            // app处理
            ontouchstart(e) {
                if (!this.canPullDown) {
                    return
                }

                this.height = 0;
                this.touchStartY = e.touches[0].pageY || e.changedTouches[0].pageY;
                this._updateRefresh(0);
                this.refreshInstance.callMethod("onchange", true);
            },
            ontouchmove(e) {
                if (!this.canPullDown) {
                    return
                }

                var oldHeight = this.height;
                var endY = e.touches[0].pageY || e.changedTouches[0].pageY;
                var newHeight = endY - this.touchStartY;
                if (newHeight > this.pullDown.maxHeight) {
                    return;
                }

                this._updateRefresh(newHeight);

                newHeight = newHeight < this.pullDown.maxHeight ? newHeight : this.pullDown.maxHeight;
                this.height = newHeight;

                this.refreshInstance.callMethod(this.pullDown.callPullingDown, {
                    height: newHeight
                });
            },
            ontouchend(e) {
                if (!this.canPullDown) {
                    return
                }

                this.refreshInstance.callMethod("onchange", false);

                if (this.height > this.pullDown.threshold) {
                    refreshInstance.callMethod(this.pullDown.callRefresh);
                    return;
                }

                this._updateRefresh(0);
            },
            _updateRefresh(height) {
                this.refreshInstance.setStyle({
                    'height': height
                });
            }
        }
    }
</script>

<style lang="scss" scoped>
    .f-list {
        height: 100%;
    }
</style>
