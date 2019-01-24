<template>
	<div>
		<el-form-item label="规格">
				<el-button  @click="addDomain">添加规格</el-button>
			<el-form-item v-for="(domain, index) in dynamicValidateForm.domains" label="" 
				:key="domain.key" 
				:prop="'domains.' + index + '.spectName'" 
    	    >
				<!--<el-input v-model="domain.value"></el-input>-->

				<el-col :span="10">
					<el-input v-model="domain.spectName" placeholder="规格名称">
						<template slot="prepend">名称</template>
					</el-input>
				</el-col>
				<el-col :span="6">
					
					<el-input v-model="domain.price" placeholder="价格">
						<template slot="prepend">价格</template>
					</el-input>
				</el-col>
				<el-col :span="6">
					<el-input v-model="domain.stock" placeholder="库存">
						<template slot="prepend">库存</template>
					</el-input>
				</el-col>
				<el-col :span="1">
					<el-button @click.prevent="removeDomain(domain)">删除</el-button>
				</el-col>
			</el-form-item>
		</el-form-item>

	</div>
</template>

<script>
	export default {
		data() {
			return {
				dynamicValidateForm: {
					domains: [{
							spectName: '',
							price: '',
							stock: ''
						}
					],
					email: ''
				}
			}
		},
		methods : {
			init(){
//				this.dynamicValidateForm.domains
			},
			removeDomain(item) {
				var index = this.dynamicValidateForm.domains.indexOf(item)
				if(index !== -1) {
					this.dynamicValidateForm.domains.splice(index, 1)
				}
			},
			addDomain() {
				this.dynamicValidateForm.domains.push({
					spectName: '',
					price: '',
					stock: ''
				});
			}
		}
	}
</script>