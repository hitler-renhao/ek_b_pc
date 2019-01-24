<template>
	<div>
		<el-form-item label="设置参数">
			<el-button @click="addDomain('')">添加</el-button>
			<el-button @click="addDomain('商品编号')">商品编号</el-button>
	    	<el-button @click="addDomain('材质')">材质</el-button>
	    	<el-button @click="addDomain('颜色')">颜色</el-button>
	    	<el-button @click="addDomain('品牌')">品牌</el-button>
	    	<el-button @click="addDomain('适用人群')">适用人群</el-button>
			<el-form-item v-for="(domain, index) in dynamicValidateForm.domains" label="" 
				:key="domain.key" :prop="'domains.' + index + '.dictKey'" >
				 <el-col :span="10">
			    	<el-input v-model="domain.dictKey"  placeholder="参数名称"><template slot="prepend">参数:{{index}}</template></el-input>
			    </el-col>
			    <el-col :span="10">
			    	<el-input v-model="domain.dictValue"  placeholder="参数值"></el-input>
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
							dictKey: '',
							dictValue: '',
						}
					]
				}
			}
		},
		methods : {
			removeDomain(item) {
				var index = this.dynamicValidateForm.domains.indexOf(item)
				if(index !== -1) {
					this.dynamicValidateForm.domains.splice(index, 1)
				}
			},
			addDomain(_name) {
				console.info(_name)
				this.dynamicValidateForm.domains.push({
					dictKey: _name,
					dictValue: '',
				});
			}
		}
	}
</script>



