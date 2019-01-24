<template>
	<div class="mod-log">
		<el-form :inline="true"  @keyup.enter.native="getDataList()">
			<!--<el-form-item>
				<el-input v-model="dataForm.productId" placeholder="编号" clearable></el-input>
			</el-form-item>
			<el-form-item label="分类" >
				<product-type ref="product_type_ref"></product-type>
	        </el-form-item>-->
			<el-form-item>
				<el-select v-model="dataForm.off" placeholder="上下架">
				  <el-option label="全部"></el-option>
			      <el-option label="上架" value="on"></el-option>
			      <el-option label="下架" value="off"></el-option>
			    </el-select>
			</el-form-item>
			<el-form-item>
				<el-button @click="getDataList()">查询</el-button>
				<el-button @click="addProduct()">新增商品</el-button>
			</el-form-item>
		</el-form>
		<el-table :data="dataList" border v-loading="dataListLoading" style="width: 100%">
			<!--<el-table-column prop="id" type="selection" header-align="center" align="center" width="150" label="编号">
			</el-table-column>-->
			<el-table-column prop="id" header-align="center" align="center" min-width="200" label="编号">
			</el-table-column>
			<el-table-column prop="productName" header-align="center" align="center" min-width="150" :show-overflow-tooltip="true" label="商品名称">
			</el-table-column>
			<el-table-column prop="url" header-align="center" align="center" min-width="80" label="商品图片">
				<template slot-scope="scope">
					<img :src="scope.row.url" alt="" style="width: 50px;height: 50px" />
				</template>
			</el-table-column>
			<!--<el-table-column prop="type"  header-align="center" align="center" width="50" label="分类">
			</el-table-column>-->
			<!--<el-table-column prop="createTime" header-align="center" align="center" width="180" label="创建时间">
			</el-table-column>-->
			<el-table-column prop="nowPrice" header-align="center" align="center" min-width="80" label="现价">
			</el-table-column>
			<el-table-column prop="sales" header-align="center" align="center" min-width="80" label="销量">
			</el-table-column>
			<el-table-column :formatter="formatterDate" header-align="center" align="center" min-width="180" label="时间">
			</el-table-column>
			<el-table-column  header-align="center" align="center" min-width="130" label="操作">
				<template slot-scope="scope">
					<el-button  type="text" size="small" @click="addProduct(scope.row.id)" >修改</el-button>
					<!--<el-button  type="text" size="small" @click="deleteHandle(scope.row.menuId)">删除</el-button>-->
					<el-button v-if="scope.row.off === 'off'" type="text" size="small" @click="offProduct_(scope.row.id,'on')">上架</el-button>
					<el-button v-if="scope.row.off === 'on'" type="text" size="small" @click="offProduct_(scope.row.id,'off')">下架</el-button>
				</template>
			</el-table-column>
		</el-table>
		<el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex"
		 :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper">
		</el-pagination>
		
		<!-- 弹窗, 新增 / 修改 -->
   		<div is="add-or-update"  v-if="addVisible" ref="addref" @refreshDataList="getDataList"></div>
	</div>
</template>

<script>
	import AddOrUpdate from './product-add-or-update'
	import productType from '@/views/modules/product/product-type'
 
	export default {
		data() {
			return {
				dataForm: {
					off : '',
					productId : '' ,
					type : '' ,
				},
				dataList: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
				selectionDataList: [],
				addVisible: false,
//				shopId:'c38f7d91-463e-40b6-8cb0-e039e2003764'				
				shopId:''				
			}
		},
		components: {
	      AddOrUpdate,
	      productType 
	   
	    },
		created() {
			this.getShopId() //初始化调用一次 
		},
		methods: {
			offProduct_(productId,off){
				console.info(productId+off)
				this.$http({
					url: this.$http.adornEkUrl('/ekProduct/offProduct'),
					method: 'post',
					params: this.$http.adornParams({
						productId : productId,
						off : off
					})
				}).then(({data}) => {
					if (data && data.code === 200) {
						this.getDataList()
					}else {
						alert(data.msg);
					} 	
					this.dataListLoading = false
				})
			},
			addProduct(id){
				if(this.shopId === '' || null == this.shopId){
//					alert('');
					this.$message({
	                  message: '没有入住店铺,不能添加商品',
	                  type: 'success',
	                  duration: 1500,
	                  onClose: () => {
//	                    this.visible = false
//	                    this.$emit('refreshDataList')
	                  }
	                })
					return
				}
				console.info('id:'+id);
				this.addVisible = true
				this.$nextTick(() => {
		          this.$refs.addref.init(this.shopId ,id)
		        })
			},
			getShopId(){
				this.dataListLoading = true
				this.$http({
					url: this.$http.adornEkUrl('/shop/queryShopByUser'),
					method: 'post',
					params: this.$http.adornParams()
					
				}).then(({
					data
				}) => {
					if (data && data.code === 200) {
						this.dataList = data.data.records
						this.totalPage = data.data.total
						this.shopId = data.data.shop.id
						this.getDataList()
					}else {
						this.dataList = []
						this.totalPage = 0
					} 	
					this.dataListLoading = false
				})
			},
			// 获取数据列表
			getDataList() {
				
				this.$http({
					url: this.$http.adornEkUrl('/ekProduct/pu/productList'),
					method: 'post',
					params: this.$http.adornParams({
						'pageNum': this.pageIndex,
						'pageSize': this.pageSize,
						'shopId': this.shopId,
						'off': this.dataForm.off,
						'productId': this.dataForm.productId,
//						'type': this.$refs.product_type_ref.type,
					})
				}).then(({	data}) => {
					if (data && data.code === 200) {
						this.dataList = data.data.records
						this.totalPage = data.data.total
					}else{
						this.dataList = []
						this.totalPage = 0
					}
				})
				this.dataListLoading = false
			},
			// 每页数
			sizeChangeHandle(val) {
				this.pageSize = val
				this.pageIndex = 1
				this.getDataList()
			},
			// 当前页
			currentChangeHandle(val) {
				this.pageIndex = val
				this.getDataList()
			},
			
			formatterDate(row, column) {
				var date =row.createTime;
			    return date.year+"-"+date.monthValue+"-"+date.dayOfYear +" "+date.hour+":"+date.second;
//    		  return "ek平台";
      		},
		}
	}
</script>

