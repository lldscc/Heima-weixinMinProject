<template>
	<!-- 轮播图组件swiper -->
	<view>
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" circular="true">
			<!-- 循环创建每个轮播图 -->
			<swiper-item v-for="(item,i) in swiperList" :key="i">
				<!-- 点击轮播图跳转到商品详情页 -->
				<navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' +item.goods_id">
					<image :src="item.image_src"></image>
				</navigator>
			</swiper-item>
		</swiper>

		<!-- 分类导航 (点击切换页面)-->
		<view class="nav-list">
			<view class="nav-item" v-for="(item,i) in navList" :key="i" @click="navClickHandler(item)">
				<image :src="item.image_src" class="nav-img"></image>
			</view>
		</view>

		<!-- 楼层数据 -->
		<view class="floor-list">
			<view class="floor-item" v-for="(item,i) in floorList" :key="i">
				<!-- 标题 -->
				<image :src="item.floor_title.image_src" class="floor-title"></image>
				<!-- 图片展示 -->
				<view class="floor-img-box">
					<!-- 左侧大盒子 -->
					<navigator class="left-img-box" :url="item.product_list[0].url">
						<image :src="item.product_list[0].image_src"
							:style="{width:item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
					</navigator>
					<!-- 右侧 4 个小图片的盒子 -->
					<view class="right-img-box">
						<navigator class="right-img-item" v-for="(item2, i2) in item.product_list" :key="i2"
							v-if="i2 !== 0" :url="item2.url">
							<image :src="item2.image_src" mode="widthFix" :style="{width:item2.image_width + 'rpx'}">
							</image>
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
				// 轮播图的数据列表
				swiperList: [],
				// 分类导航的数据列表
				navList: [],
				// 楼层数据
				floorList: []
			}
		},
		onLoad() {
			// 调用方法，获取轮播图数据
			this.getSwiperList()
			this.getNavList()
			this.getFloorList()
		},
		methods: {
			// 定义getSwiperList，获取API
			async getSwiperList() {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/home/swiperdata')
				// 请求失败
				if (res.meta.status !== 200) return uni.$showMsg()

				this.swiperList = res.message
			},


			// 定义getSwiperList，获取API
			async getNavList() {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/home/catitems')
				if (res.meta.status !== 200) return uni.$showMsg()
				this.navList = res.message

			},
			//定义getFloor，获取API
			async getFloorList() {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/home/floordata')
				if (res.meta.status !== 200) return uni.$showMsg()

				//通过双层 forEach 循环，处理 URL 地址
				res.message.forEach(floor => {
					floor.product_list.forEach(prod => {
						prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
					})
				})

				this.floorList = res.message
			},

			// 点击事件(跳转到指定页面，)
			navClickHandler(item) {
				// console.log(item)
				// tabar页面，用switchTab
				if (item.name === '分类') {
					uni.switchTab({
						url: '/pages/cate/cate'
					})
				}
			}


		},


	}
</script>

<style lang="scss">
	swiper {
		height: 330rpx;

		.swiper-item,
		image {
			width: 100%;
			height: 100%;

		}
	}

	.nav-list {
		display: flex;
		justify-content: space-around;
		margin: 15px 0;

		.nav-img {
			width: 128rpx;
			height: 140rpx;
		}
	}

	.floor-title {
		height: 60rpx;
		width: 100%;
		display: flex;
	}

	.right-img-box {
		display: flex;
		flex-wrap: wrap;
		justify-content: space-around;
	}

	.floor-img-box {
		display: flex;
		padding-left: 10rpx;
	}
</style>