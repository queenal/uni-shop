<template>
	<view>
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
			<swiper-item v-for="(item, i) in swiperList" :key="i">
				<navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
					<image :src="item.image_src" />
				</navigator>
			</swiper-item>
		</swiper>
		<view class="nav-list">
			<view class="nav-item" v-for= "(item, i) in navList" :key="i">
				<image class="nav-img" :src="item.image_src" @click="navClickHandler(item)" />
			</view>
		</view>
		<view class="floor-list">
			<view v-for="(item, i) in floorList" >
				<image class="floor-title" :src="item.floor_title.image_src" />
				<view class="floor-img-box">
					<navigator :url="item.product_list[0].url" class="left-img-box">
						<image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}"  mode="widthFix"></image>
					</navigator>
					<view class="right-img-box">
						<navigator :url="type.url" class="right-box-item" v-for="(type, index) in item.product_list" :key="index" v-if="index!==0 ">
							<image :src="type.image_src" :style="{width: type.image_width + 'rpx'}"  mode="widthFix" />
						</navigator>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				swiperList :[],
				navList: [],
				floorList: []
			};
		},
		onLoad() {
			this.getSwiperList()
			this.getNavList()
			this.getFloorList()
		},
		methods:{
			async getSwiperList() {
				const {data: res} = await uni.$http.get('/api/public/v1/home/swiperdata')
				// if(res.meta.status !== 200) {
				// 	return uni.showToast({
				// 		title: '数据加载失败',
				// 		duration: 1500,
				// 		icon: 'none'
				// 	})
				// }
				if(res.meta.status !== 200) uni.$message()
				this.swiperList = res.message
			},
			async getNavList() {
				const {data: res} = await uni.$http.get('/api/public/v1/home/catitems')
				if(res.meta.status !== 200) uni.$message()
				this.navList = res.message
			},
			navClickHandler(item) {
				if(item.name === '分类') {
					uni.switchTab({
						url: '/pages/cate/cate'
					})
				}
			},
			async getFloorList() {
				const {data: res} = await uni.$http.get('/api/public/v1/home/floordata')
				if(res.meta.status !== 200) uni.$message()
				this.floorList = res.message
				
				this.floorList.forEach(item=>{
					item.product_list.forEach(prod=>{
						prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
					})
				})
			},
		}
	}
</script>

<style lang="scss">
	swiper{
		height: 300rpx;
		
		.swiper-item ,
		image{
			height: 100%;
			width: 100%;
		}
	}
	.nav-list{
		display: flex;
		justify-content:space-around;
		 margin: 15px 0;
		.nav-item{
			  .nav-img {
			    width: 128rpx;
			    height: 140rpx;
			  }
		}
	}
	.floor-title{
		width: 100%;
		height: 60rpx;
	}
	.floor-img-box{
		display: flex;
		padding-left: 10rpx;
	}
	.right-img-box{
		display: flex;
		justify-content: space-around;
		flex-wrap: wrap;
		flex-direction: row;
	}
</style>
