<template>
	<el-select v-model="type"  placeholder="类型">
	  <el-option label="全部" value='0' ></el-option>
      <el-option label="镜框" value="2"></el-option>
      <el-option label="墨片" value="3"></el-option>
      <el-option label="太阳镜" value="5"></el-option>
      <el-option label="老花镜" value="7"></el-option>
      <el-option label="隐形眼镜" value="6"></el-option>
    </el-select>
</template>

<script>
	export default {
		data() {
			return {
				type: ''
			}
		}
	}
</script>
