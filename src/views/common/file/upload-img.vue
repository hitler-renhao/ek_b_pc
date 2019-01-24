<template>
	<div>
		<el-upload :action="action"
			:http-request="requestUpload"
			:data="data" 
			list-type="picture-card" 
			:on-preview="handlePictureCardPreview" 
			:on-success="handleSuccess" 
			:before-upload="beforeUpload" 
			:file-list="result"
			:on-remove="handleRemove"
			:limit="limit"
			>
			<i class="el-icon-plus"></i>
		</el-upload>
		<el-dialog :visible.sync="dialogVisible">
			<img width="100%" :src="dialogImageUrl" alt="">
		</el-dialog>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				dialogImageUrl: '',
				dialogVisible: false,
				action: '' ,
				data : {
					tokenKey:''
				},
				result:[] //返回�?
			};
		},
		created() {
			this.init()
			
		},
    props: ['limit'],
		methods: {
			init() {
//				`/sys/oss/upload?token=${this.$cookie.get('token')}`
//				var url ='/comment/uploadImg?tokenKey='+;
				this.action=this.$http.adornEkUrl('/comment/uploadImg')//
				this.data.tokenKey=this.$cookie.get('ek_tokenKey')
//				console.info(this.action)
			},
			handleRemove(file, fileList) {
				console.log("ucommpent-pload-img----"+file, fileList);
				this.result.pop(file)
				console.log("移除 以后 "+JSON.stringify(this.result))
			},
			handleSuccess(response, file, fileList) {
				var dd=response.data[0]
				if(response.code === 200){
					this.result.push({url:dd})
				}
//				console.log(this.result)
			},
			handlePictureCardPreview(file) {
				this.dialogImageUrl = file.url;
				this.dialogVisible = true;
				console.info(this.dialogImageUrl)
			},
			beforeUpload(file) {
//				console.log(file)

				return true
			},
			requestUpload(item){
				console.info('0000')    
				let formData = new FormData();
      			formData.append('file', item.file);
      			formData.append('tokenKey', this.data.tokenKey);
      							
				let config = {
		            headers:{'Content-Type':'multipart/form-data'}
		        };  //添加请求头
		        this.$axios.post(this.action,formData,config)
		          .then(response=>{
		            console.log(response.data);
		          }) 
				}	
		}	
		
	}
</script>
