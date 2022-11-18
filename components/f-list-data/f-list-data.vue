<template>
	<view class="bg-grey-3 page" style="overflow-y: unset">
        <template v-if="listData && listData.length">
            <view class="q-px-15 q-mt-15">
				<slot name="data" :listData="listData"></slot>
            </view>
            <f-load-more v-if="listData?.length > this.limit" :status="loadStatus"></f-load-more>
        </template>
		<template v-else-if="listData && listData.length === 0">
			<f-empty mode="data" text="数据为空!"></f-empty>
		</template>
	</view>
</template>

<script>
	export default {
		props: {
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
				limit: 30,
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
        mounted(e) {
            this.loadData(false);
        },
        // 监听用户下拉动作，下拉刷新
        onPullDownRefresh() {
            this.loadData(true);
        },
        // 页面滚动到底部的事件（不是scroll-view滚到底）
        onReachBottom() {
            this.loadData(false);
        },
        methods: {
            async loadData(refresh) {
                if (!refresh) {
                    if (this.isLoading) {
                        return;
                    }

                    if (this.page - 1 >= this.totalPage) {
                        this.loadStatus = 'noMore';
                        return;
                    }
                }

                this.loadStatus = 'loading';
                this.isLoading = true;

                const response = await this.$api.get(this.apiUrl, {
                    limit: this.limit,
                    page: refresh ? 1 : ++this.page
                }, this.isAuth)
                let listData = response.list;
                this.totalPage = Math.ceil(response.count / this.limit);

                this.isLoading = false;
                if (this.totalPage === 1) {
                    this.loadStatus = 'noMore';
                }

				if (refresh) {
                    this.listData = listData;
                    this.page = 1;
                    this.totalPage = 1;
                    // 结束下拉
                    uni.stopPullDownRefresh();
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
:deep(f-list-data) {
	display: flex;
	flex: 1;
	flex-direction: column;
}
</style>
