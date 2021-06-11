<template>
	<view class="content">
		<movable-area class="mapContent">
			<movable-view direction='all' friction='6' inertia class="moveView">
				<image class="bgImg" mode="heightFix" @load="imgLoad" src="https://636f-co-872-1259111184.tcb.qcloud.la/cokBG.png?sign=d34be9c807f8969f95b38bb05d9d9fbd&t=1606227930"></image>
				<!-- <map-item v-for="(item,i) in arr" :key="i" :position="item" bind:isMask="isMask" class="point"></map-item> -->
				<map-item v-for="(item,i) in arr" :key="i" :style="{top:item.h+'px',left:item.w+'px'}" class="point" @tap="tapPoint(item)"></map-item>
			</movable-view>
		</movable-area>
		<!-- 点击建筑后弹出的蒙版 -->
		<view wx:if="{{mask}}" class="mask" @tap="cancelMask">
			<view class="mapItems">
				<view class="btn mapitem" v-for="(item,i) in datas" :key="i">
					<view data-key="{{i}}" @tap="turnItem(item)">{{i}}</view>
				</view>
			</view>
			<!-- 广告 -->
			<view>
				<ad unit-id="adunit-43a4e2d32e476972" ad-intervals="30"></ad>
			</view>
		</view>
	</view>
</template>

<script>
import mapItem from '../../components/mapItem.vue'
	export default {
		components:{
			mapItem
		},
		data() {
			return {
				displayList:[],
				list:[],
				i:0,
				scrollbar:true,
				displayTimer:0,
				ratioH:1,
				ratioW:1,
				arr:[],
				datas:[],//每个建筑的独有数据
				mapData:{},//地图数据
				mask:false//蒙版显示
			}
		},
		watch:{
			arr:function(){
				this.arr.map((item)=>{
					item.h=item.h*this.ratioH;
					item.w=item.w*this.ratioH;
				})
				return this.arr
			}
		},
		methods:{
			//加载图片
			imgLoad(){
				const query = uni.createSelectorQuery().in(this);
				query.select('.bgImg').fields({size:true},res=>{
				//原始尺寸
				const width=3223;
				const height=1080;
				if(res){
					//获取当前尺寸
					const nowH =res.height;
					const nowW =res.width;
					//根据原始尺寸计算比例
					this.ratioH=nowH/height;
					this.ratioW=nowW/width;
				}
				}).exec();
			},
			//取消蒙版
			cancelMask(){
				this.mask=false;
			},
			//弹出蒙版，返回标题
			// 点击point事件
			tapPoint(item){
				this.datas = this.mapData[item.name];
				this.mask = true;
			},
			//跳转子页面
			turnItem(item){
				console.log(item);
				const id=e.currentTarget.dataset.key;
				console.log(id);
				let value=this.mapItem[id];
				value=JSON.stringify(value);
				wx.navigateTo({
				url: '/pages/mapItem/mapItem?id='+id+'&value='+value,
				})
			}
		},
		onLoad: function (options) {
			var that = this;
			wx.cloud.init();
			const db = wx.cloud.database();
			const table=db.collection('cok');
			table.doc('index').get({
				success:function(res){
					that.displayList=res.data.display;
					that.arr=res.data.mapList;
					console.log(that.arr);
				}
			})
			const map = db.collection('map');
			map.get({
				success:function(res){
				that.mapData=res.data[0];
				console.log('that.mapData',that.mapData);
			}
			})
		},
	}
</script>

<style scoped>
	.mapContent{
		position: fixed;
		top:0;
		left: 0;
		overflow: hidden;
		width: 100%;
		height:100%;
	}
	.moveView{
		width: 3223px;
		height: 1080px;
	}
	.bgImg{
		height: 100%;
		width: 100%;
	}
	/* 点 */
	.point{
		position: absolute;
		width: 50rpx;
		height: 50rpx;
		border: 1px solid white;
	}
	/* 展播 */
	.display{
		position: fixed;
		bottom: 48rpx;
		left: 48rpx;
		width: 400rpx;
		height: 200rpx;
		font-size: 12px;
		overflow: hidden;
		color: white;
	}
	/* 蒙版 */
	.mask{
		position: fixed;
		width: 100%;
		height:100%;
		background-color: rgb(0,0,0,0.7);
	}
	/* 每个建筑数据 */
	.mapItems{
		width: 100%;
		position: absolute;
		bottom: 0;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		color: white;
	}
	.mapitem{
		height:56rpx;
		font-size: 18px;
		line-height: 56rpx;
	}
</style>
