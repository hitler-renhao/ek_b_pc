<template>
	<div class="box">
	<el-form ref="form" :model="form" label-width="80px" size="mini" class="formbox">

		<el-form-item label="套餐名称">
			<el-col :span="8">
				<el-input v-model="form.name"></el-input>
			</el-col>
		</el-form-item>
		<el-form-item label="套餐说明">
			<el-col :span="8">
				<el-input v-model="form.explains"></el-input>
			</el-col>
		</el-form-item>
		<el-form-item label="门市价">
			<el-col :span="8">
				<el-input v-model="form.price"></el-input>
			</el-col>
		</el-form-item>
		<el-form-item label="平台价">
			<el-col :span="8">
				<el-input v-model="form.nowPrice"></el-input>
			</el-col>
		</el-form-item>

		<!--购买须知-->
		<el-form-item label="购买须知">
			<el-col :span="10">
				<el-input type="textarea" v-model="form.content"></el-input>
			</el-col>
		</el-form-item>
		<!--套餐明细-->
		<addmealInfo ref="addmealInfoRef" is="addmeal-info"></addmealInfo>
		<!--套餐有效期-->
		<el-form-item label="套餐有效期">
			<el-row style="width:300px;height:50px;">
				<el-input type="text" v-model="form.alltime"></el-input>
			</el-row>
			<el-row style="width:300px;">
				<el-input type="text" v-model="form.retime"></el-input>
			</el-row>
			<!--<el-radio v-model="form.timess" label="1">一个月</el-radio>
			<el-radio v-model="form.timess" label="3">三个月</el-radio>
			<el-radio v-model="form.timess" label="6">六个月</el-radio>-->
		</el-form-item>
		<!--<el-form-item label="套餐有效期">
				<el-radio-group v-model="form.timess">
					<el-radio label="一个月" value="1"></el-radio>
					<el-radio label="三个月" value="3"></el-radio>
					<el-radio label="六个月" value="6"></el-radio>
				</el-radio-group>
			</el-form-item>-->
		<!--套餐图片-->
		<el-form-item label="套餐图片">
			<div is="upload-img" ref="productImgRef"></div>
		</el-form-item>
		<!--套餐详情介绍图-->
		<el-form-item label="套餐详情介绍图">
			<div is="upload-img" ref="productImgRef2"></div>
		</el-form-item>

		<!--提交按钮-->
		<el-form-item size="large">
			<el-button type="primary" @click="onSubmit()" class="btn">{{offdel}}</el-button>
		</el-form-item>
		<div class="bigbox"></div>
	</el-form>
	</div>
</template>

<script>
	import uploadImg from '@/views/common/file/upload-img'
	import addmealInfo from '@/views/modules/combo/addmeal_info'
	export default {
		components: {
			uploadImg,
			addmealInfo
		},
		data() {
			return {
				visible: false,
				pic: {},
				mealUrl: [],
				offdel: '',
				form: {
					mealId: this.$route.query.id,
					shopId: this.$route.query.shopId,
					name: '',
					explains: '',
					price: '',
					nowPrice: '',
					content: '',
					imgInfo: '',
					mealInfo: '',
					timess: '',
					off: '1',
					alltime: '',
					retime: ''
				},
				dynamicValidateForm: {
					domains: [{
						specName: '',
						specSum: '',
						specCompany: '',
						specPrice: ''
					}],
					email: ''
				}
			};
		},
		created() {
			this.mealdel()
		},

		methods: {
			/*套餐明细*/
			mealdel() {
				//this.$refs.addmealInfoRef.dynamicValidateForm.domains = []
				//this.$refs.productImgRef.result = []
				//this.$refs.productImgRef2.result = []
				this.$http({
					url: this.$http.adornEkUrl('/ekSetMeal/querySetMealinfo'),
					method: 'post',
					params: this.$http.adornParams({
						setmealid: this.form.mealId
					})
				}).then(({
					data
				}) => {
					console.log(data);
					if(data && data.code === 200) {
						const res = data.data;
						this.form.name = res.setmeal.name;
						this.form.explains = res.setmeal.explains;
						this.form.price = res.setmeal.price;
						this.form.nowPrice = res.setmeal.nowPrice;
						this.form.content = res.setmeal.content;
						if(res.setmeal.off == '0') {
							this.offdel = '上架此套餐';
						} else if(res.setmeal.off == '1') {
							this.offdel = '下架此套餐';
						}
						//套餐图片回显
						for(var i = 0; i < res.setmealFile.length; i++) {
							if(res.setmealFile[i].type == '1') {
								this.$refs.productImgRef.result.push({
									url: res.setmealFile[i].url,
									orders: res.setmealFile[i].orders,
									type: res.setmealFile[i].type,
								})
							} else if(res.setmealFile[i].type == '2') {
								this.$refs.productImgRef2.result.push({
									url: res.setmealFile[i].url,
									orders: res.setmealFile[i].orders,
									type: res.setmealFile[i].type,
								})
							}
						}
						//套餐明细回显
						for(var j = 0; j < res.setmealAtt.length; j++) {
							this.$refs.addmealInfoRef.dynamicValidateForm.domains.push({
								specName: res.setmealAtt[j].specName,
								specCompany: res.setmealAtt[j].specCompany,
								specPrice: res.setmealAtt[j].specPrice,
								specSum: res.setmealAtt[j].specSum
							})
						}
						//有效期
						const comboTime = res.setmeal.time;
						const endTime = new Date(comboTime).getTime();
						const startTime = new Date().getTime();
						//console.log(format(endTime));
						this.form.retime = '到期时间: ' + this.format(endTime);
						const surplus = ((endTime - startTime) / 1000 / 60 / 60 / 24).toFixed(0) + '天';
						this.form.alltime = '剩余时间: ' + surplus;
					}
				})
			},
			dub(m) {
				return m < 10 ? '0' + m : m
			},
			format(shijianchuo) {
				//shijianchuo是整数，否则要parseInt转换
				const time = new Date(shijianchuo);
				const y = time.getFullYear();
				const m = time.getMonth() + 1;
				const d = time.getDate();
				const h = time.getHours();
				const mm = time.getMinutes();
				const s = time.getSeconds();
				return y + '年' + this.dub(m) + '月' + this.dub(d) + '日';

			},
			onSubmit() {
				if(this.offdel == '上架此套餐') {
					this.combo('1', '上架架套餐成功')
				} else if(this.offdel == '下架此套餐') {
					this.combo('0', '下架架套餐成功')
				}
			},
			/*上下架套餐*/
			combo(off, message) {
				this.$http({
					url: this.$http.adornEkUrl('/ekSetMeal/offSetMeal'),
					method: "post",
					headers: {
						'Content-Type': 'application/x-www-form-urlencoded'
					},
					data: this.$http.adornData({
						off: off,
						setmealid: this.form.mealId
					}, true, 'false')
				}).then(({
					data
				}) => {
					if(data && data.code === 200) {
						this.$message({
							message: '下架套餐成功',
							type: 'success',
							duration: 1500,
						})
						this.$router.push({
							path: '/combo-comboList'
						});
					} else {
						this.msg_(data.msg)
					}
				})
			},
			validata_() {

				//console.log(this.mealUrl)
				//				if(this.form.name == '' || this.form.name == null) {
				//					this.msg_("套餐名称不能为空");
				//					return false
				//				}else if(this.form.explains == '' || this.form.explains == null){
				//					this.msg_("套餐说明不能为空");
				//					return false
				//				}else if(this.form.price == '' || this.form.price == null){
				//					this.msg_("门市价不能为空");
				//					return false
				//				}else if(this.form.nowPrice == '' || this.form.nowPrice == null){
				//					this.msg_("平台价不能为空");
				//					return false
				//				}else if(this.form.content == '' || this.form.content == null){
				//					this.msg_("购买须知不能为空");
				//					return false
				//				}else if(this.form.timess == '' || this.form.timess == null){
				//					this.msg_("请选择有效期");
				//					return false
				//				}
				//				return true
			},
			msg_(msg__) {
				this.$message({
					message: msg__,
					type: 'success',
					duration: 1500,
					onClose: () => {
						//          this.visible = false
						//	        this.$emit('refreshDataList')
					}
				})
			},
			//封装图片 格式  为想要的 格式
			getImgJson(imgJson_, productImgRef, type) {
				for(var i = 0; i < productImgRef.length; i++) {
					console.info('i:' + productImgRef[i])
					imgJson_.push({
						type: type,
						url: productImgRef[i].url,
						orders: i
					})
				}
			}
		}
	};
</script>

<style scoped>
	.box{min-width:500px;max-width:850px;margin:0 auto}
	.namebox {
		margin-right: 20px;
	}
	
	.bigbox {
		width: 100%;
		height: 100%;
		position: absolute;
		top: 0;
		left: 0;
		z-index: 1000;
	}
	
	.btn {
		position: absolute;
		z-index: 10000;
	}
	
	.formbox {
		position: relative;
		z-index: 100;
		padding-bottom: 50px;
	}
</style>