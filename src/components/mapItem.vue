<template>
	<view class="content">
        <view class="title">{{id}}</view>
        <view class="flex">
            <view class="content2" wx:for="{{value}}" wx:for-index="index" wx:key="index" wx:for-item="value">
            {{value}}
            </view>
        </view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
                id:'',
                value:null
			}
		},
        onLoad: function (options) {
            var that = this;
            var value=JSON.parse(options.value);
            that.id=options.id;
            that.value=value;
        },
	}
</script>

<style scoped>
    .content2{
        border: none;
        border-radius: 0;
        border-bottom: 1px solid white;
        font-size: 15px;
        line-height: 30rpx;
        padding:10rpx;
        margin:0;
        text-indent: 0;
        float: left;
        clear: both;
    }
    .flex{
        display: flex;
        align-items: center;
        justify-content: center;
        flex-direction: column;
    }
</style>
