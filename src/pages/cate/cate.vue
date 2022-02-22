<template>
	<view>
		<view class="scroll-view-container">
			<!-- 左侧滚动区域 -->
			<scroll-view class="left-scroll-view" scroll-y :style="{height: wh + 'px'}">
				<block  v-for="(item, index) in cateList" :key="index">
						<view :class="['left-scroll-item', index === active ? 'active' : '']" @click="activeChanged(index)">{{item.cat_name}}</view>
				</block>
			</scroll-view>
			<!-- 右侧滚动区域 -->
			<scroll-view class="right-scroll-view" scroll-y :style="{height: wh + 'px'}">
				<view class="cate-level2" v-for="(item2, index2) in cateLevel2">
					<view class="cate-level2-title">/ {{' '}}{{item2.cat_name}} {{' '}}/</view>
					<view class="cate-level3-list">
						<view class="cate-level3-item" v-for="(item3, index3) in item2.children" :key="index3" @click=gotoGoodsList(item3)>
							<image :src="item3.cat_icon"></image>
							<text>{{item3.cat_name}}</text>
						</view>
					</view>
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				wh: 0,
				cateList:[],
				active: 0,
				cateLevel2: [],
				scrollTop: 0
			};
		},
		onLoad() {
			const sysInfo = uni.getSystemInfoSync()
			this.wh = sysInfo.windowHeight
			this.getCateList()
		},
		methods:{
			async getCateList(){
				const {data: res} = await uni.$http.get('/api/public/v1/categories')
				if(res.meta.status !== 200 ) uni.$showMsg()
				this.cateList = res.message
				// 请求接口之后，默认选择首项
				this.cateLevel2 = res.message[0].children
			},
			activeChanged(index){
				this.active = index 
				// 点击后为二级分类重新赋值
				this.cateLevel2 = this.cateList[index].children
				
				// 跳转之后会重新回到顶部
				this.scrollTop = this.scrollTop ? 1 : 0
			},
			gotoGoodsList(item3){
				uni.navigateTo({
					url: '/subpkg/goods_list/goods_list?cid=' + item3.cat_id
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.scroll-view-container{
		display: flex;
		.left-scroll-view{
			width: 120px;
			.left-scroll-item{
				line-height: 60px;
				background-color: #f7f7f7;
				text-align: center;
				font-size: 12px;
				&.active {
					background: #fff;
					position: relative;
					&::before{
						content: '';
						display: block;
						width: 3px;
						height: 30px;
						background-color: #c00000;
						position: absolute;
						left: 0;
						top: 50%;
						transform:translateY(-50%);
					}
				}
			}
		}
		.right-scroll-view{
			.cate-level2 {
				.cate-level2-title{
					 font-size: 12px;
					  font-weight: bold;
					  text-align: center;
					  padding: 15px 0;
				}
			}
			.cate-level3-list{
				display: flex;
				flex-direction: row;
				flex-wrap: wrap;
				.cate-level3-item{
					width: 33.3%;
					// flex: 1;
					display: flex;
					flex-direction: column;
					align-items: center;
					
					
					   image {
					      width: 60px;
					      height: 60px;
					    }
					
					    text {
					      font-size: 12px;
					    }
				}
			}
		}
}
	

</style>
