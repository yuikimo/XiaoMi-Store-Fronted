<!--
 * @Description: 列表组件，用于首页、全部商品页面的商品列表
-->
<template>
	<div id="myList" class="myList">
		<el-skeleton :loading="list === ''" animated :throttle="500"
			style="width: 100%; background-color: white;">
			<template slot="template">
				<div style="display: flex; flex-direction: row;padding: 10px 5px; margin: 0 5px; gap: 10px">
					<el-skeleton-item variant="image"
						style="width: 240px; height: 240px;" />
					<el-skeleton-item variant="image"
						style="width: 240px; height: 240px;" />
					<el-skeleton-item variant="image"
						style="width: 240px; height: 240px;" />
					<el-skeleton-item variant="image"
						style="width: 240px; height: 240px;" />
				</div>
				<div style="display: flex; flex-direction: row; gap: 5px; padding: 10px 5px; margin: 0 5px;">
					<div
						style="padding-top: 10px; padding-left: 3px; width: 240px;">
						<el-skeleton-item variant="h3" style="width: 155px;" />
						<div
							style="display: flex; align-items: center; justify-items: space-between; margin-top: 8px; height: 16px;">
							<el-skeleton-item variant="text"
								style="margin-right: 10px;" />
						</div>
					</div>
					<div
						style="padding-top: 10px; padding-left: 3px; width: 240px;">
						<el-skeleton-item variant="h3" style="width: 155px;" />
						<div
							style="display: flex; align-items: center; justify-items: space-between; margin-top: 8px; height: 16px;">
							<el-skeleton-item variant="text"
								style="margin-right: 10px;" />
						</div>
					</div>
					<div
						style="padding-top: 10px; padding-left: 3px; width: 240px;">
						<el-skeleton-item variant="h3" style="width: 155px;" />
						<div
							style="display: flex; align-items: center; justify-items: space-between; margin-top: 8px; height: 16px;">
							<el-skeleton-item variant="text"
								style="margin-right: 10px;" />
						</div>
					</div>
					<div
						style="padding-top: 10px; padding-left: 3px; width: 240px;">
						<el-skeleton-item variant="h3" style="width: 155px;" />
						<div
							style="display: flex; align-items: center; justify-items: space-between; margin-top: 8px; height: 16px;">
							<el-skeleton-item variant="text"
								style="margin-right: 10px;" />
						</div>
					</div>
				</div>
			</template>
			<template slot="default">
				<ul>
					<li v-for="item in list" :key="item.productId">
						<el-popover placement="top">
							<p>确定删除吗？</p>
							<div style="text-align: right; margin: 10px 0 0">
								<el-button type="primary" size="mini"
									@click="deleteCollect(item.productId)">确定</el-button>
							</div>
							<i class="el-icon-close delete" slot="reference"
								v-show="isDelete">
							</i>
						</el-popover>
						<router-link
							:to="{ path: '/goods/details', query: { productID: item.productId } }">
							<img :src="item.productPicture" alt />
							<h2>{{ item.productName }}</h2>
							<h3>{{ item.productTitle }}</h3>
							<p>
								<span>{{ item.productSellingPrice }}元</span>
								<span
									v-show="item.productPrice != item.productSellingPrice"
									class="del">{{ item.productPrice }}元
								</span>
							</p>
						</router-link>
					</li>
				</ul>
			</template>
		</el-skeleton>
	</div>
</template>
<script>
export default {
	name: "MyList",
	// list为父组件传过来的商品列表
	// isMore为是否显示“浏览更多”
	props: ["list", "isMore", "isDelete"],
	data() {
		return {
			// loading: false,
		};
	},
	computed: {
		// 通过list获取当前显示的商品的分类ID，用于“浏览更多”链接的参数
		categoryID: function () {
			let categoryID = [];
			if (this.list != "") {
				for (let i = 0; i < this.list.length; i++) {
					const id = this.list[i].category_id;
					if (!categoryID.includes(id)) {
						categoryID.push(id);
					}
				}
			}
			return categoryID;
		}
	},
	// watch: {
	// 	list(val) {
	// 		if (val !== null) {
	// 			this.loading = true;
	// 		}
	// 	}
	// },
	methods: {
		async deleteCollect(product_id) {
			await this.$axios
				.delete("/api/collect/user/" + product_id + "/" + this.$store.getters.getUser.userId)
				.then(res => {
					switch (res.data.code) {
						case "001":
							// 删除成功
							// 删除删除列表中的该商品信息
							for (let i = 0; i < this.list.length; i++) {
								const temp = this.list[i];
								if (temp.productId == product_id) {
									this.list.splice(i, 1);
								}
							}
							// 提示删除成功信息
							this.notifySucceed(res.data.msg);
							break;
						default:
							// 提示删除失败信息
							this.notifyError(res.data.msg);
					}
				})
				.catch(err => {
					return Promise.reject(err);
				});
		}
	}
};
</script>
<style scoped>
.myList ul li {
	z-index: 1;
	float: left;
	width: 234px;
	height: 280px;
	padding: 10px 0;
	margin: 0 0 14.5px 13.7px;
	background-color: white;
	-webkit-transition: all 0.2s linear;
	transition: all 0.2s linear;
	position: relative;
}

.myList ul li:hover {
	z-index: 2;
	-webkit-box-shadow: 0 15px 30px rgba(0, 0, 0, 0.1);
	box-shadow: 0 15px 30px rgba(0, 0, 0, 0.1);
	-webkit-transform: translate3d(0, -2px, 0);
	transform: translate3d(0, -2px, 0);
}

.myList ul li img {
	width: 160px;
	display: block;
	margin: 0 auto;
}

.myList ul li h2 {
	margin: 25px 10px 0;
	font-size: 14px;
	font-weight: 400;
	color: #333;
	text-align: center;
	text-overflow: ellipsis;
	white-space: nowrap;
	overflow: hidden;
}

.myList ul li h3 {
	margin: 5px 10px;
	height: 18px;
	font-size: 12px;
	font-weight: 400;
	color: #b0b0b0;
	text-align: center;
	text-overflow: ellipsis;
	white-space: nowrap;
	overflow: hidden;
}

.myList ul li p {
	margin: 10px 10px 10px;
	text-align: center;
	color: #ff6700;
}

.myList ul li p .del {
	margin-left: 0.5em;
	color: #b0b0b0;
	text-decoration: line-through;
}

.myList #more {
	text-align: center;
	line-height: 280px;
}

.myList #more a {
	font-size: 18px;
	color: #333;
}

.myList #more a:hover {
	color: #ff6700;
}

.myList ul li .delete {
	position: absolute;
	top: 10px;
	right: 10px;
	display: none;
}

.myList ul li:hover .delete {
	display: block
}

.myList ul li .delete:hover {
	color: #ff6700;
}
</style>