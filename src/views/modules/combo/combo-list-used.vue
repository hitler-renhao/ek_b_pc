<template>
	<div id="comboListUsed">
		<el-row>
			<el-col :span="24">
				<!--<div class="grid-content bg-purple-dark">
          <span class="hasUsed">已使用套餐列表</span>
          <span class="useCombo" @click="useCombo = true">使用套餐</span>
        </div>-->
			</el-col>
		</el-row>
		<el-form @keyup.enter.native="useCombos()">
			<el-form-item label="识别优惠码" :label-width="formLabelWidth" style="width:660px;">
				<el-input v-model="comboCode" autocomplete="off" class="codeNumber" style="width:360px;"></el-input>
				<!--<el-button @click="useCombo = false" style="margin-left:10px">取 消</el-button>-->
				<el-button type="primary" @click="useCombos" style="margin-left:10px">确 定</el-button>
			</el-form-item>
		</el-form>
		<div slot="footer" class="dialog-footer">
			<!--<el-button @click="useCombo = false">取 消</el-button>
          <el-button type="primary" @click="useCombos">确 定</el-button>-->
		</div>
		<!-- 列表 -->
		<div id="list">
			<el-table :data="dataList" border style="width: 100%">
				<el-table-column prop="id" label="编号" align="center" min-width="50px">
				</el-table-column>
				<el-table-column prop="specName" label="套餐券名称" align="center" min-width="50px">					
				</el-table-column>
				<el-table-column prop="specSum" label="套餐券数量" align="center" min-width="30px">
				</el-table-column>
				<el-table-column prop="specPrice" label="套餐券价格" align="center" min-width="30px">
				</el-table-column>
				<!--<el-table-column prop="time" label="使用时间">
				</el-table-column>-->
			</el-table>

			<!-- 使用套餐弹框 -->
			<!--<el-dialog title="使用套餐券" :visible.sync="useCombo">-->

			<!--</el-dialog>-->
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				productInfo: [{
				}],
				formLabelWidth: 'auto',
				useCombo: false,
				comboCode: '',				
				tokenKey:'',
				userType:'',
				dataList: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				currentPage1: 1,
				loading: false,				
			}
		},
		methods: {
			getmealList(){
				this.$http({
					url: this.$http.adornEkUrl('/ekSetMealOrder/querySetmealOrder'),
					method: 'post',
					params: this.$http.adornParams({
						tokenKey:this.$cookie.get('ek_tokenKey'),
						userType:'use',
						pageNum:this.pageIndex,
						pageSize:this.pageSize
					})
				})
				.then((data) => {
					console.log(data);
					if (data && data.code === 200) {
						this.dataList = data.data.records
						this.totalPage = data.data.total
					}else{
						this.dataList = []
						this.totalPage = 0
					}
				})
				.catch(function(error) {
					console.log(error);
				});
			},
			useCombos() {
				if(!this.comboCode) {
					this.$message.error('请填写套餐优惠码');
				} else {
					this.cancelOrder = false;
					this.$http({
							url: this.$http.adornEkUrl('/ekSetMealOrder/setMealOrderDetails'),
							method: 'post',
							params: this.$http.adornParams({
								code: this.comboCode
							})
						})
						.then((response) => {
							if(response.data.code == 200) {								
								this.$message({
									message: '套餐券识别成功',
									type: 'success'
								});
								this.dataList = data.data.setmealinfo.setmealAtt;
								//this.getmealList();
							} else if(response.data.code == 400) {
								this.$message.error('套餐优惠码已过期或无效优惠码!');
								this.dataList = [];
							}
						})
				}
			}
		},
	}
</script>

<style scoped lang="scss">
	.el-row {
		margin-bottom: 20px;
		&:last-child {
			margin-bottom: 0;
		}
	}
	
	.el-col {
		border-radius: 4px;
	}
	
	.bg-purple-dark {
		background: #99a9bf;
	}
	
	.grid-content {
		border-radius: 4px;
		min-height: 36px;
	}
	
	.row-bg {
		padding: 10px 0;
		background-color: #f9fafc;
	}
	
	.hasUsed {
		font-size: 20px;
		line-height: 40px;
		color: #f9fafc;
		font-weight: 700;
	}
	
	.useCombo {
		position: absolute;
		right: 20px;
		color: #fff;
		font-size: 16px;
		line-height: 40px;
		border: none;
	}
</style>