<template>
	<el-form-item label="套餐明细">
		<el-row>
			<el-button id="" icon="el-icon-circle-plus-outline" @click="addDomain">新增项</el-button>
		</el-row>
		<el-row>
			<el-col :span="6" class="namebox">
				名称
			</el-col>
			<el-col :span="3" class="namebox">
				数量
			</el-col>
			<el-col :span="3" class="namebox">
				单位
			</el-col>
			<el-col :span="3" class="namebox">
				价格
			</el-col>
		</el-row>
		<el-row :span="20" v-for="(domain, index) in dynamicValidateForm.domains" label="" :key="domain.key" :prop="'domains.' + index + '.specName'" :rules="{
    	      required: true, trigger: 'blur'
    	    }">
			<el-col :span="6" algin="center" class="namebox">
				<el-input placeholder="请输入名称" v-model="domain.specName"></el-input>
			</el-col>
			<el-col :span="3" algin="center" class="namebox">
				<el-input placeholder="请输入数量" v-model="domain.specSum"></el-input>
			</el-col>
			<el-col :span="3" algin="center" class="namebox">
				<el-input placeholder="请输入单位" v-model="domain.specCompany"></el-input>
			</el-col>
			<el-col :span="3" algin="center" class="namebox">
				<el-input placeholder="请输入价格" v-model="domain.specPrice"></el-input>
			</el-col>
			<el-col :span="3" algin="center" class="namebox">
				<el-button @click.prevent="removeDomain(domain)">删除</el-button>
			</el-col>
		</el-row>
	</el-form-item>
</template>

<script>
	export default {
		data() {
			return {
				dynamicValidateForm: {
					domains: [{
						specName: '',
						specSum: '',
						specCompany: '',
						specPrice: ''
					}],
					email: ''
				}
			}
		},
		methods : {
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
		}
	}
</script>
<style scoped>
	.namebox {
		margin-right: 20px;
	}
</style>