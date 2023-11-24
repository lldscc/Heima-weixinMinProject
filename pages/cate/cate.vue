<template>
	<!-- 分类页 -->
	<view>
		<view class="scroll-view-container">
			<!-- scroll-view滑动视图组件 -->
			<!-- 左侧的滑动视图区域 -->
			<scroll-view class="left-scroll-view" scroll-y="true" :style="{height:wh+'px'}">
				<block v-for="(item, i) in cateList" :key="i">
					<view :class="['left-scroll-view-item', i === active ? 'active' : '']" @click="activeChanged(i)">
						{{item.cat_name}}
					</view>
				</block>
				<!-- <block v-for="(item, i) in cateList2" :key="i">
					<view class="left-scroll-view-item">{{item}}</view>
				</block> -->

			</scroll-view>


			<!-- 右侧的滑动视图区域 -->
			<scroll-view class="right-scroll-view" scroll-y="true" :style="{height: wh +'px'}">
				<view class="cate-lv2" v-for="(item2, i2) in cateLevel2" :key="i2">
					<!-- 二级分类的标题 -->
					<view class="cate-lv2-title">/ {{item2.cat_name}} /</view>
					<!-- 三级分类的内容 -->
					<view class="cate-lv3-list">
						<view class="cate-lv3-item" v-for="(item3, i3) in item2.children" :key="i3"
							@click="gotGoodsList(item3)">
							<!-- 图片 -->
							<image :src="item3.cat_icon"></image>
							<!-- 文本 -->
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
				active: 0,
				scrollTop: 0,
				// 1.获取分类数据  定义数组存放一级分类的数据
				cateList: [],
				// 获取二级分类的数据
				cateLevel2: [],

			};
		},
		methods: {
			// 3.获取分类数据:定义方法
			async getCateList() {
				// 发起请求
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/categories')
				if (res.meta.status !== 200) return uni.$showMsg()
				// 赋值一级分类的数据
				this.cateList = res.message
				// 赋值二级分类的数据
				this.cateLevel2 = res.message[0].children

			},
			// 点击事件
			activeChanged(i) {
				this.active = i
				// 为二级分类列表重新赋值
				this.cateLevel2 = this.cateList[i].children

			},
			// 跳转到商品列表页
			gotGoodsList(item) {
				uni.navigateTo({
					url: '/subpkg/goods_list/goods_list?cid=' + item.cat_id
				})

			}



		},
		onLoad() {
			// 调用API：getSystemInfoSync，windowHeight获取可使用窗口高度
			const sysInfo = uni.getSystemInfoSync()
			this.wh = sysInfo.windowHeight
			// 2.获取分类数据:调用获取分类列表数据的方法
			this.getCateList()

		},
	}
</script>

<style lang="scss">
	.scroll-view-container {
		display: flex;

		.left-scroll-view {
			width: 120px;


			.left-scroll-view-item {
				background-color: #F7F7F7;
				line-height: 60px;
				text-align: center;
				font-size: 12px;

				// &表示父选择器
				&.active {
					background-color: #FFFFFF;
					position: relative;

					// 设置前条
					&::before {
						content: ' ';
						display: block;
						width: 3px;
						height: 30px;
						background-color: #C00000;
						position: absolute;
						top: 50%;
						left: 0;
						transform: translateY(-50%)
					}
				}



			}
		}


		.right-scroll-view {



			.cate-lv2-title {
				font-size: 12px;
				font-weight: bold;
				text-align: center;
				padding: 15px 0;
			}

			.cate-lv3-list {
				display: flex;
				flex-wrap: wrap;

				.cate-lv3-item {
					width: 33.33%;
					margin-bottom: 10px;
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