<template>
	<div class="mod-log">
		<el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
			<!--<el-form-item>
				<el-input v-model="dataForm.key" placeholder="订单编号" clearable></el-input>
			</el-form-item>
			<el-form-item>
				<el-input v-model="dataForm.key" placeholder="收货人" clearable></el-input>
			</el-form-item>
			<el-form-item>
				<el-input v-model="dataForm.key" placeholder="提交时间" clearable></el-input>
			</el-form-item>-->
			<el-form-item>
				<el-button @click="getDataList()">查询</el-button>
			</el-form-item>
		</el-form>
		<el-table :data="dataList" border v-loading="dataListLoading" style="min-width: 100%">
			<el-table-column prop="id" header-align="center" align="center" min-width="150" label="订单编号">
			</el-table-column>
			<el-table-column prop="createTime" header-align="center" align="center" label="提交时间">
			</el-table-column>
			<el-table-column prop="buyUserName" header-align="center" align="center" min-width="80" :show-overflow-tooltip="true" label="用户账号">
			</el-table-column>
			<el-table-column prop="orderTotalPrice" header-align="center" align="center" min-width="80" :show-overflow-tooltip="true" label="订单金额">
			</el-table-column>
			<!-- <el-table-column prop="time" header-align="center" align="center" label="支付方式">
			</el-table-column> -->
			<el-table-column :formatter="formatter"  header-align="center" align="center" min-width="80" label="订单来源">
			</el-table-column>
			<el-table-column :formatter="formatterType" header-align="center" align="center" min-width="80" label="订单状态">
			</el-table-column>
			<el-table-column  header-align="center" align="center" min-width="80" label="操作">
				<template slot-scope="scope">
					<el-button type="text" size="small" @click="info_method(scope.row.id)">查看</el-button>
					<el-button v-if="scope.row.orderStatus === '5'" type="text" size="small" @click="deleteHandle(scope.row.menuId)">跟踪</el-button>
					<!--<el-button v-if="isAuth('sys:menu:delete')" type="text" size="small" @click="deleteHandle(scope.row.menuId)">删除</el-button>-->
					<el-button v-if="scope.row.orderStatus === '2'" type="text" size="small" @click="deleteHandle(scope.row.menuId)">关闭</el-button>
					<el-button v-if="scope.row.orderStatus === '4'" type="text" size="small" @click="deleteHandle(scope.row.menuId)">发货</el-button>
				</template>
			</el-table-column>
		</el-table>
		<el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex"
		 :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper">
		</el-pagination>
		
		<!-- 弹窗, 新增 / 修改 -->
   		<div is="info"  v-if="infoVisible" ref="inforef" @refreshDataList="getDataList"></div>
	</div>
</template>

<script>
	import info from './order-info'
	export default {
		data() {
			return {
				dataForm: {
					key: ''
				},
				dataList: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
				selectionDataList: [],
				infoVisible: false
			}
		},
		components: {
	      info
	    },
		created() {
			this.getDataList()
		},
		methods: {
			info_method(id){
				console.info('id:'+id);
				this.infoVisible = false
				this.$nextTick(() => {
							this.$router.push({ path: '/order-order-info', query: {id: id} })
		        })
			},
			// 获取数据列表
			getDataList() {
				this.dataListLoading = true
				this.$http({
					url: this.$http.adornEkUrl('/ekProductOrder/list'),
					method: 'post',
					params: this.$http.adornParams({
						'pageNum': this.pageIndex,
						'pageSize': this.pageSize,
						'isShop': 'shop',
						// 'key': this.dataForm.key,
					})
				}).then(({
					data
				}) => {
					if (data && data.code === 200) {
						this.dataList = data.data.records
						this.totalPage = data.data.total
					}else if (4400 === data){
						alert("没有 token   token过期 ek公众号项目  请重新登录");
						
					}else {
						this.dataList = []
						this.totalPage = 0
						alert("没数据");
						
					} 
					this.dataListLoading = false
				})
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
			
			formatter(row, column) {
      		  return "ek平台";
      		},
			formatterType(row, column) {
				console.info("___"+row.orderStatus)
				var x=0;
				var status=parseInt(row.orderStatus);
				switch (status){
					case -2:
						x = "已退款";
						break
					case -1:
						x= "退款中";
						break
					case 0:
						x= "已取消";
						break
					case 1:
						x= "已完成";
						break
					case 2:
						x= "代付款";
						break
					case 3:
						x= "已付款";
						break
					case 4:
						x= "已接单";
						break
					case 5:
						x= "已发货";
						break
					case 6:
						x= "已收货";
						break
				}
				return x;
			}
		}
	}
</script>
