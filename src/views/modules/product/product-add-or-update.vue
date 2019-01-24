<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
    custom-class="mydialog"
    width="60%"
    >
				<el-form   ref="dataForm" :rules="dataRule"  @keyup.enter.native="dataFormSubmit()" label-width="50px">
			      
			      <el-form-item label="类别" prop="type" >
							<el-input is="product-type" ref="typeRef"></el-input>
			      </el-form-item>
			      <el-form-item label="商品" >
			      	<el-col :span="10">
					    	<el-input v-model="dataForm.productName"   placeholder="名称"><template slot="prepend">名称</template></el-input>
					    </el-col>
					    <el-col :span="6">
					    	<el-input v-model="dataForm.beginPrice"  placeholder="门市价"><template slot="prepend">门市价</template></el-input>
					    </el-col>
					    <el-col :span="6">
					    	<el-input v-model="dataForm.nowPrice"  placeholder="平台价"><template slot="prepend">平台价</template></el-input>
					    </el-col>
			      </el-form-item>
			      <product-spec ref="productSpec_ref"></product-spec>
						<product-param ref="productparam_ref"></product-param>     

						<el-form-item label="商品图片" >
					  	<div is="upload-img" ref="productImgRef"></div>
					  </el-form-item>  
					  <el-form-item label="商品详情图" >
					  	<div is="upload-img" ref="productInfoImgRef"></div>
					  </el-form-item>  
					  <el-form-item label="商品介绍图" >
					  	<div is="upload-img" ref="productShowImgRef"></div>
					  </el-form-item>  
		    </el-form>
			
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import { isEmail, isMobile } from '@/utils/validate'
  import productType from '@/views/modules/product/product-type'
  import productSpec from '@/views/modules/product/product-spec'
  import productParam from '@/views/modules/product/product-param'
  import uploadImg from '@/views/common/file/upload-img'

  
export default {
  	components: {
	      productType ,
	      uploadImg ,
	      productSpec,
	      productParam
	  },
    data () {
      return {
        visible: false,
        roleList: [],
        dataForm: {
          id: '',
          productId: '',
          shopId: '',
          type: '',
          productName: '',
          beginPrice: '',
          nowPrice: '',
          imgJson: '',
          specJson: '',
          paramJson: '',
          status: '' ,
        }
      }
    },
    methods: {
      init (shopId,id) {
      	this.visible = true
      	this.$nextTick(() => {
	  			this.$refs.typeRef.type=''
	  			this.$refs.productImgRef.result=[]
	  			this.$refs.productInfoImgRef.result=[]
	  			this.$refs.productShowImgRef.result=[]
	  			this.$refs.productparam_ref.dynamicValidateForm.domains=[]
	  			this.$refs.productSpec_ref.dynamicValidateForm.domains=[]
	      	
	      	if(id === null || id== '' || id ==undefined){
						console.info(123)      		
	      		this.dataForm={}
	      		this.dataForm.shopId=shopId
	      	}else{
	      		this.dataForm.shopId=shopId
	      		this.$http({
							url: this.$http.adornEkUrl('/ekProduct/pu/productInfo'),
							method: 'post',
							params: this.$http.adornParams({
								productId : id
							})
						}).then(({data}) => {
							if (data && data.code === 200) {
								var data=data.data
								this.dataForm.productId=data.product.id
								this.dataForm.id=data.product.id
								this.dataForm.productName=data.product.productName
								this.dataForm.nowPrice=data.product.nowPrice
								this.dataForm.beginPrice=data.product.beginPrice
								this.$refs.typeRef.type=data.product.type
								this.$refs.productSpec_ref.dynamicValidateForm.domains=data.productSpec
								this.$refs.productparam_ref.dynamicValidateForm.domains=data.productParam
								for(var i=0;i<data.productFile.length;i++){
									if(data.productFile[i].type== 1){
										this.$refs.productImgRef.result.push({
											url:data.productFile[i].url,
											id:data.productFile[i].id,
											type:data.productFile[i].type,
										})
									}
									if(data.productFile[i].type== 2){
										this.$refs.productInfoImgRef.result.push({
											url:data.productFile[i].url,
											id:data.productFile[i].id,
											type:data.productFile[i].type,
										})
									}
									if(data.productFile[i].type== 3){
										this.$refs.productShowImgRef.result.push({
											url:data.productFile[i].url,
											id:data.productFile[i].id,
											type:data.productFile[i].type,
										})
									}
								}
	//							console.info("img:"+JSON.stringify(this.$refs.productImgRef.result))
							}else {
								alert(data.msg);
							}
							this.dataListLoading = false
						})
	      	}
      	})
      },
      // 表单提交
      dataFormSubmit () {
      	this.dataForm.type=this.$refs.typeRef.type  //类型
      	
      	this.dataForm.paramJson=JSON.stringify(this.getParamJson(this.$refs.productparam_ref.dynamicValidateForm.domains))//参数
      	this.dataForm.specJson=JSON.stringify(this.getSpecJson(this.$refs.productSpec_ref.dynamicValidateForm.domains)) //
      	
				var imgJson_=[]
				this.getImgJson(imgJson_,this.$refs.productImgRef.result,1)//主图
				this.getImgJson(imgJson_,this.$refs.productInfoImgRef.result,2)//详情
				this.getImgJson(imgJson_,this.$refs.productShowImgRef.result,3)//介绍图 
				
				this.dataForm.imgJson=JSON.stringify(imgJson_)

				//验证
	      if(!this.validata_() ){
	      	return
	      }
	      
	      let config = {
		            headers:{'Content-Type': 'application/x-www-form-urlencoded'}
		        }; 
	      //post请求 格式  
	      this.$axios.post(this.$http.adornEkUrl(`/ekProduct/${!this.dataForm.id ? 'addProduct' : 'updateProduct'}`)
	      		,this.$http.adornData(this.dataForm,true,'false'),
	      		config
	      		)
//	      this.$http({
//						url: ,
//						method: 'post',
//						headers:,
//						data: 
//				})
	      .then(({data}) => {
						if (data && data.code === 200) {
		            this.$message({
		              message: '操作成功',
		              type: 'success',
		              duration: 1500,
		              onClose: () => {
		                this.visible = false
		                this.$refs.dataForm.resetFields()
		                this.$emit('refreshDataList')
		              }
		            })
		        } else {
		            this.msg_(data.msg)
		        }
				})
      },
      validata_(){
      	if(this.dataForm.type == 0 || this.dataForm.type ==null){
	      		this.msg_("类别不能为空");
      			return false
      	}
      	if(this.dataForm.productName == '' || this.dataForm.type ==null){
	      		this.msg_("商品名称不能为空");
      			return false
      	}
      	return true
      },
      msg_(msg__){
      	this.$message.error({
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
      getImgJson(imgJson_,productImgRef,type){
      	for(var i=0;i<productImgRef.length;i++){
					console.info('i:'+productImgRef[i])
					imgJson_.push({
						type:type,
						url:productImgRef[i].url,
						orders:i
					})
				}
      },
      getParamJson(paramJson){
      	var json_=[]
      	for(var i=0;i<paramJson.length;i++){
      		json_.push({
      			dictKey:paramJson[i].dictKey,
      			dictValue:paramJson[i].dictValue,
      		})
      	}
      	return json_
      },
      getSpecJson(specJson){
      	var json_=[]
      	for(var i=0;i<specJson.length;i++){
      		json_.push({
      			spectName:specJson[i].spectName,
      			stock:specJson[i].stock,
      			price:specJson[i].price,
      		})
      	}
      	return json_
      }
      
    }
  }
</script>

<style scoped>
	/*修饰 element ui 需要 加上 >>>  */
	>>> .mydialog {
		width : 700px ;
	}
</style>