<!--
 * @Description: 首页组件
-->
<template>
	<div class="home" id="home" name="home">
		<!-- v-if="phoneList !== '' && miTvList !== '' &&
		protectingShellList !== '' && chargerList !== ''
		&& accessoryList !== ''" -->
		<!-- 轮播图 -->
		<div class="block">
			<el-skeleton style="width: 100%; background: white;" :loading="carousel === ''" animated
				:throttle="500">
				<template slot="template">
					<el-skeleton-item variant="image"
						style="width: 100%; height: 460px;" />
				</template>

				<template>
					<el-carousel height="460px">
						<el-carousel-item v-for="item in carousel"
							:key="item.carouselId">
							<img style="height:460px;" :src="item.imgPath"
								:alt="item.describes" />
						</el-carousel-item>
					</el-carousel>
				</template>
			</el-skeleton>
		</div>
		<!-- 轮播图END -->

		<div class="main-box">
			<div class="main">
				<!-- 手机商品展示区域 -->
				<div class="phone">
					<div class="box-hd">
						<div class="title">手机特卖</div>
					</div>
					<div class="box-bd">
						<div class="promo-list">
							<router-link to>
								<img :src="images.phone_phone" />
							</router-link>
						</div>
						<div class="list">
							<MyList :list="phoneList" :isMore="true">
							</MyList>
						</div>
					</div>
				</div>
				<!-- 手机商品展示区域END -->

				<!-- 电视商品展示区域 -->
				<div class="phone">
					<div class="box-hd">
						<div class="title">电视影音</div>
					</div>
					<div class="box-bd">
						<div class="promo-list">
							<router-link to>
								<img :src="images.appliance_watch" />
							</router-link>
						</div>
						<div class="list">
							<MyList :list="miTvList" :isMore="true"></MyList>
						</div>
					</div>
				</div>
				<!-- 电视商品展示区域END -->

				<!-- 配件商品展示区域 -->
				<div class="accessory" id="promo-menu">
					<div class="box-hd">
						<div class="title">热门商品</div>
						<div class="more" id="more">
							<MyMenu :val="3" @fromChild="getChildMsg2">
								<span slot="1">热门</span>
								<span slot="2">风扇</span>
								<span slot="3">音响</span>
							</MyMenu>
						</div>
					</div>
					<div class="box-bd">
						<div class="promo-list">
							<ul>
								<li>
									<img :src="images.accessory_promo1" alt />
								</li>
								<li>
									<img :src="images.accessory_promo2" alt />
								</li>
							</ul>
						</div>
						<div class="list">
							<MyList :list="accessoryList" :isMore="true">
							</MyList>
						</div>
					</div>
				</div>
				<!-- 配件商品展示区域END -->
			</div>
		</div>
	</div>
</template>
<script>
export default {
	data() {
		return {
			loading: true,
			carousel: "", // 轮播图数据
			phoneList: "", // 手机商品列表
			miTvList: "", // 小米电视商品列表
			// applianceList: "", // 家电商品列表
			// applianceHotList: "", //热门家电商品列表
			accessoryList: "", //配件商品列表
			accessoryHotList: "", //热门配件商品列表
			protectingShellList: "", // 保护套商品列表
			chargerList: "", //充电器商品列表
			applianceActive: 1, // 家电当前选中的商品分类
			accessoryActive: 1, // 配件当前选中的商品分类
			images: {
				phone_phone: require("../../static/public/imgs/phone/phone.png"),
				appliance_watch: require("../../static/public/imgs/appliance/Watch-S3.png"),
				accessory_promo1:
					require("../../static/public/imgs/accessory/accessory-promo1.png"),
				accessory_promo2:
					require("../../static/public/imgs/accessory/accessory-promo2.png"),
			}
		};
	},
	watch: {
		// // 家电当前选中的商品分类，响应不同的商品数据
		// applianceActive: function(val) {
		//   // 页面初始化的时候把applianceHotList(热门家电商品列表)直接赋值给applianceList(家电商品列表)
		//   // 所以在切换商品列表时判断applianceHotList是否为空,为空则是第一次切换,把applianceList赋值给applianceHotList
		//   if (this.applianceHotList == "") {
		//     this.applianceHotList = this.applianceList;
		//   }
		//   if (val == 1) {
		//     // 1为热门商品
		//     this.applianceList = this.applianceHotList;
		//     return;
		//   }
		//   if (val == 2) {
		//     // 2为电视商品
		//     this.applianceList = this.miTvList;
		//     return;
		//   }
		// },
		accessoryActive: function (val) {
			// 页面初始化的时候把accessoryHotList(热门配件商品列表)直接赋值给accessoryList(配件商品列表)
			// 所以在切换商品列表时判断accessoryHotList是否为空,为空则是第一次切换,把accessoryList赋值给accessoryHotList
			if (this.accessoryHotList == "") {
				this.accessoryHotList = this.accessoryList;
			}
			if (val == 1) {
				// 1为热门商品
				this.accessoryList = this.accessoryHotList;
				return;
			}
			if (val == 2) {
				// 2为保护套商品
				this.accessoryList = this.protectingShellList;
				return;
			}
			if (val == 3) {
				//3 为充电器商品
				this.accessoryList = this.chargerList;
				return;
			}
		}
	},
	async created() {
		// 获取轮播图数据
		await this.$axios
			.get("/api/resources/carousel")
			.then(res => {
				this.carousel = res.data.data;
			})
			.catch(err => {
				return Promise.reject(err);
			});
		// 获取各类商品数据
		await this.getPromo(1, "phoneList"); // 手机列表
		await this.getPromo(2, "miTvList"); // TV列表
		await this.getPromo(5, "protectingShellList"); // 风扇列表
		await this.getPromo(7, "chargerList");  // 音响列表
		await this.getHot("accessoryList"); // 热销列表
	},
	methods: {
		// 获取家电模块子组件传过来的数据
		// getChildMsg(val) {
		//   this.applianceActive = val;
		// },
		// 获取配件模块子组件传过来的数据
		getChildMsg2(val) {
			this.accessoryActive = val;
		},
		// 获取各类商品数据方法封装
		async getPromo(categoryId, val) {
			let api = "/api/product/category/limit/" + categoryId;
			await this.$axios
				.get(api)
				.then(res => {
					this[val] = res.data.data;
				})
				.catch(err => {
					return Promise.reject(err);
				});
		},
		async getHot(val) {
			let api = "/api/product/category/hot";
			await this.$axios
				.get(api)
				.then(res => {
					this[val] = res.data.data;
				})
				.catch(err => {
					return Promise.reject(err);
				});
		}
	}
};
</script>
<style scoped>
@import "../assets/css/index.css";
</style>