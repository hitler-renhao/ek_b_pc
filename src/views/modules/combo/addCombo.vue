<template>

	<el-form ref="form" :model="form" label-width="80px" size="mini" style="min-width:500px;max-width:850px;margin:0 auto">
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
			<el-radio v-model="form.timess" label="1">一个月</el-radio>
			<el-radio v-model="form.timess" label="3">三个月</el-radio>
			<el-radio v-model="form.timess" label="6">六个月</el-radio>
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
			<div is="upload-img" ref="productImgRef" :limit='1'></div>
		</el-form-item>
		<!--套餐详情介绍图-->
		<el-form-item label="套餐详情介绍图">
			<div is="upload-img" ref="productImgRef2" :limit='3'></div>
		</el-form-item>
		<!--提交按钮-->
		<el-form-item size="large">
			<el-button type="primary" @click="onSubmit()">提交</el-button>
		</el-form-item>
	</el-form>

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
				limit1:1,
				limit2:3,
				form: {
					shopId: this.$route.query.shopId,
					name: '',
					explains: '',
					price: '',
					nowPrice: '',
					content: '',
					off: '',
					imgInfo: '',
					mealInfo: '',
					timess: '',
					off: '1',
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
		created(){				
			//this.$refs.productImgRef.limit == 1
			//this.$refs.productImgRef2.limit == 3
		},
		methods: { 			
			
			onSubmit() {
				/*获取套餐明细*/

				this.form.mealInfo = JSON.stringify(this.$refs.addmealInfoRef.dynamicValidateForm.domains) //套餐明细      	
				console.info(JSON.parse(this.form.mealInfo));
				//this.form.mealInfo = JSON.parse(this.form.mealInfo);
				//console.log(this.form.mealInfo)
				//    			const meals = JSON.parse(this.form.mealInfo)
				//				console.log(meals);
				/*获取套餐详情 图*/
				var imgJson_ = []
				this.getImgJson(imgJson_, this.$refs.productImgRef.result, 1) //主图
				this.getImgJson(imgJson_, this.$refs.productImgRef2.result, 2) //详情
				this.form.imgInfo = JSON.stringify(imgJson_)
				//				this.form.imgInfo = JSON.stringify(imgJson_)
				//				const mealimg = JSON.stringify(this.$refs.productImgRef.result);
				//				console.log(mealimg);
				//				const mealInfoimg = JSON.stringify(this.$refs.productImgRef2.result);
				//				//console.info(JSON.parse(mealInfoimg);
				//				const mealimg2 = JSON.parse(mealimg);
				//				const mealInfoimg2 = JSON.parse(mealInfoimg);
				//				const allmealImg = mealimg2.concat(mealInfoimg2);
				//console.log(allmealImg.length);
				//				for(var i = 0; i < allmealImg.length; i++) {
				//					//console.log(allmealImg[i]);
				//					if(i == 0) {
				//						var indexx = JSON.stringify(1)
				//					} else {
				//						var indexx = JSON.stringify(2)
				//					}
				//					//console.log(indexx);
				//					this.pic = {
				//						type: indexx,
				//						url: allmealImg[i],
				//						orders: i
				//					}
				//				}
				//				this.mealUrl.push(this.pic);
				//				this.form.imgInfo = JSON.stringify(this.mealUrl)
				//				if(!this.validata_()) {
				//					return
				//				}
				//console.log(1);
				//验证
		        if(!this.validata_() ){
		      		return
		      	}
				this.$http({
					url: this.$http.adornEkUrl('/ekSetMeal/addSetMeal'),
					method: "post",
					headers: {
						'Content-Type': 'application/x-www-form-urlencoded'
					},
					data: this.$http.adornData(this.form, true, 'false')
				}).then(({
					data
				}) => {
					console.log(data);
					if(data && data.code === 200) {
						this.$message({
							message: '添加套餐成功',
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
				if(this.form.name == '' || this.form.name == null) {
					this.msg_("套餐名称不能为空");
					return false
				}else if(this.form.explains == '' || this.form.explains == null){
					this.msg_("套餐说明不能为空");
					return false
				}else if(this.form.price == '' || this.form.price == null){
					this.msg_("门市价不能为空");
					return false
				}else if(this.form.nowPrice == '' || this.form.nowPrice == null){
					this.msg_("平台价不能为空");
					return false
				}else if(this.form.content == '' || this.form.content == null){
					this.msg_("购买须知不能为空");
					return false
				}else if(this.$refs.addmealInfoRef.dynamicValidateForm.domains == '' || this.$refs.addmealInfoRef.dynamicValidateForm.domains == null ){
					this.msg_("请完整填写套餐明细");
					return false
				}else if(this.form.timess == '' || this.form.timess == null){
					this.msg_("请选择有效期");
					return false
				}else if(this.$refs.productImgRef.result == '' || this.$refs.productImgRef.result == null){
					this.msg_("请上传套餐图片");
					return false
				}else if(this.$refs.productImgRef.limit == 1){
					this.msg_("套餐图片只能上传一张");
					return false
				}else if(this.$refs.productImgRef2.result == '' || this.$refs.productImgRef2.result == null){
					this.msg_("请上传套餐详情图");
					return false
				}else if(this.$refs.productImgRef2.limit == 3){
					this.msg_("套餐详情图最多能上传3张");
					return false
				}
				return true
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
		},

		//删除套餐明细
		removeDomain(item) {
			var index = this.dynamicValidateForm.domains.indexOf(item)
			if(index !== -1) {
				this.dynamicValidateForm.domains.splice(index, 1)
			}
		},
		//添加套餐明细
		addDomain() {
			this.dynamicValidateForm.domains.push({
				specName: '',
				specSum: '',
				specCompany: '',
				specPrice: ''
			});
		}

	};
</script>

<style scoped>
	.namebox {
		margin-right: 20px;
	}
	
	.el-form-item__label {
		width: 100px;
	}
	
	.avatar-uploader .el-upload {
		border: 1px dashed #d9d9d9;
		border-radius: 6px;
		cursor: pointer;
		position: relative;
		overflow: hidden;
	}
	
	.avatar-uploader .el-upload:hover {
		border-color: #409EFF;
	}
	
	.avatar-uploader-icon {
		font-size: 28px;
		color: #8c939d;
		width: 148px;
		height: 148px;
		line-height: 148px;
		text-align: center;
	}
	
	.avatar {
		width: 148px;
		height: 148px;
		display: block;
	}
	
	.avatar-uploader .el-upload {
		border: 1px dashed #d9d9d9;
		border-radius: 6px;
		cursor: pointer;
		position: relative;
		overflow: hidden;
	}
	
	.avatar-uploader .el-upload:hover {
		border-color: #409EFF;
	}
	
	.avatar-uploader-icon {
		font-size: 28px;
		color: #8c939d;
		width: 148px;
		height: 148px;
		line-height: 148px;
		text-align: center;
	}
	
	.avatar {
		width: 148px;
		height: 148px;
		display: block;
	}
</style>