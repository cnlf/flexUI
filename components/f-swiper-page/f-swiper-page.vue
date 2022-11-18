<template>
    <f-list ref="list" @scrollToLower="loadData" @refresh="loadData(true)">
        <!-- 刷新 -->
        <f-refresh ref="refresh" :isLoading="isLoading" @refresh="loadData(true)"></f-refresh>

        <!--列表-->
        <f-cell>
            <view class="q-px-15" v-if="listData && listData.length">
                <slot name="data" :listData="listData"></slot>
            </view>
            <view v-else class="flex-center flex-full">
                <text class="font-16 mt-1">{{ listData === null ? '' : '暂无内容...' }}</text>
            </view>
        </f-cell>

        <!-- 加载更多 -->
        <f-cell>
            <f-load-more v-if="listData?.length > this.limit" :status="loadStatus"></f-load-more>
        </f-cell>
    </f-list>
</template>

<script>
export default {
    name: 'f-swiper-page',
    props: {
        pageInfo: {
            type: Object,
            default: () => {
            }
        },
        limit: {
            type: Number,
            default: 30
        },
        apiUrl: {
            type: String,
            required: true
        },
        isAuth: {
            type: Boolean,
            defaultValue: false
        }
    },
    data() {
        return {
            isLoading: false,
            loadStatus: 'more',
            listData: null,
            page: 0,
            totalPage: 0,
        }
    },
    created() {
        if (!this.apiUrl) {
            uni.showModal({
				content: '接口信息必须填写！',
				showCancel: false
            })
        }
    },
    methods: {
        async loadData(refresh, fromHome) {
            // tab-bar滑动 并且 已经加载过数据的页面不需要再加载数量
            if (fromHome && this.listData) {
                return;
            }

            if (!refresh) {
                if (this.isLoading) {
                    return;
                }

                if (this.page - 1 >= this.totalPage) {
                    this.loadStatus = 'noMore';
                    return;
                }
            }

            // 停止当前页面下拉刷新。
            if (uni?.stopPullDownRefresh) {
                uni.stopPullDownRefresh();
            }

            this.loadStatus = 'loading';
            this.isLoading = true;

            const response = await this.$api.get(this.apiUrl, {
                limit: this.limit,
                page: refresh ? 1 : ++this.page
            }, this.isAuth);
            const listData = response.list;
            this.totalPage = response.totalPage;

            this.isLoading = false;
            if (this.totalPage === 1) {
                this.loadStatus = 'noMore';
            }

            // 刷新
            if (refresh) {
                this.listData = listData;
                this.page = 1;
                this.totalPage = 1;
                // 结束下拉
                this.$refs.list.stop();
            } else {
                // 加载分页
                if (this.listData === null) {
                    this.listData = [];
                }
                this.listData = this.listData ? this.listData.concat(listData) : listData;
            }
        },
    }
}
</script>

<style lang="scss">
:deep(f-swiper-page) {
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
}
</style>
