<template>
	<div class="bigbox">
		<!--添加套餐-->
		<el-form :inline="true" :model="dataForm" @keyup.enter.native="comboList()">
			<el-row>
				<el-select placeholder="分类" v-model="dataForm.off">
					<el-option label="已上架" value="1"></el-option>
					<el-option label="已下架" value="0"></el-option>
				</el-select>
				<el-button align="left" class="check" @click="comboList()">查询</el-button>
				<el-button align="right" class="addCombo" icon="el-icon-circle-plus-outline" @click="addC">添加套餐</el-button>				
			</el-row>
		</el-form>
		<!--套餐列表-->
		<el-table :data="tableData" border style="width: 100%" v-loading="loading">
			<el-table-column prop="setmeal.id" type="selection" header-align="center" align="center" min-width="20px" label="编号">
			</el-table-column>
			<el-table-column prop="setmeal.id" label="编号" min-width="60px" align="center">
			</el-table-column>
			<el-table-column prop="setmeal.name" label="套餐名称" min-width="50px" align="center">
			</el-table-column>
			<el-table-column prop="file.url" label="套餐图片" align="center" min-width="50px">
				<template slot-scope="scope">
					<img :src="scope.row.file.url" alt="" style="width: 50px;height: 50px;display:inline-block" />
				</template>
			</el-table-column>
			<el-table-column prop="setmeal.nowPrice" label="套餐价格" align="center" min-width="30px">
			</el-table-column>
			<el-table-column :formatter="createT" label="添加时间" align="center" min-width="50px">
			</el-table-column>
			<el-table-column label="操作" align="center" min-width="50px">
				<template slot-scope="scope">
					<el-button type="text" @click="editMeal(scope.row.setmeal.id)">修改</el-button>
					<!--<el-button type="text">删除</el-button>-->
					<!--<el-button type="text" @click="offProduct_(scope.row.setmeal.id,'1')">上架</el-button>-->
					<el-button type="text" @click="offProduct_(scope.row.setmeal.id,'0')">下架</el-button>
				</template>
			</el-table-column>
		</el-table>
		<el-pagination background @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="currentPage1" :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" layout="total, sizes, prev, pager, next, jumper" :total="totalPage">
		</el-pagination>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				dataForm: {
					off: '1',
					setmealId:''
				},
				tableData: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				currentPage1: 1,
				loading: false,
				shopId: '',
			}
		},
		//生命周期
		created() {
			this.getshopId()
		},
		methods: {
			//上下架
			offProduct_(setmealId,off){
				console.info(setmealId+off)
				this.$http({
					url: this.$http.adornEkUrl('/ekSetMeal/offSetMeal'),
					method: 'post',
					params: this.$http.adornParams({
						setmealid : setmealId,
						off : off
					})
				}).then(({data}) => {
					console.log(data);
					if (data && data.code === 200) {
						this.comboList()
						this.$message({
							message: '下架成功',
							type: 'success',
							duration: 1500,							
						})
					}else {
						alert(data.msg);
					} 	
					this.dataListLoading = false
				})
			},
			//编辑套餐
			editMeal(id) {
				if(this.shopId == '' || this.shopId == null) {
					//console.log(1);
					this.$message({
						message: '没有入住店铺,不能添加套餐',
						type: 'success',
						duration: 1500,
						onClose: () => {
						
						}
					})
					return
				}
				console.info('id:' + id);				
				this.$router.push({
					path: '/combo-editCombo',
					query: {
			            id: id,
			            shopId: this.shopId
			        }
				});
			},
			getshopId(){
				this.$http({
					url: this.$http.adornEkUrl('/shop/queryShopByUser'),
					method: 'post',
					params: this.$http.adornParams({
						
					})					
				}).then(({
					data
				}) => {
					console.log(data);
					if (data && data.code === 200) {
						//console.log(data);						
						if(!data.data.shop){
							this.shopId = '';
						}else{
							this.shopId = data.data.shop.id	
						}
						this.comboList()
					}else {
						this.tableData = []
						this.totalPage = 0
					} 						
				})
			},
			comboList() {
				//this.loading = true;
				this.$http({
					url: this.$http.adornEkUrl('/ekSetMeal/pu/list'),
					method: 'post',
					params: this.$http.adornParams({
						'pageNum': this.pageIndex,
						'pageSize': this.pageSize,
						'shopId': this.shopId,
						'off': this.dataForm.off
					})
				})
				.then(( response ) => {
					console.log(response)
					if(response.data && response.data.code === 200) {
						//console.log(data);
						this.tableData = response.data.data;
						this.totalPage = response.data.data.length
						
						//this.setmeal.id = data.data[0].setmeal.id
					} else {
						this.tableData = []
						this.totalPage = 0
					}
				})
			},
			// 每页数
			handleSizeChange(val) {
				this.pageSize = val
				this.pageIndex = 1
				this.comboList()
			},
			// 当前页
			handleCurrentChange(val) {
				this.pageIndex = val
				this.comboList()
			},
			//添加套餐跳转
			addC() {
				if(this.shopId == '' || this.shopId == null) {
					console.log(1);
					this.$message({
						message: '没有入住店铺,不能添加套餐',
						type: 'success',
						duration: 1500,
						onClose: () => {
						
						}
					})
					return
				}
				//console.log(1);
				this.$router.push({
					path: '/combo-addCombo',
					query: {
			            shopId: this.shopId
			          }
				});
			},
			createT(row, column){
				let time = row.setmeal.time;
				return time.split('T')[0]+'　'+time.split('T')[1]
				//console.log(time.split('T')[0]);
			}
		}
	}
</script>

<style lang="scss" scoped>
	.addCombo {
		float: right;
		margin-bottom: 10px;
	}
</style>