<template>
  <div class="account-details">
    <el-table :data="tableData" border style="width: 100%">
      <el-table-column min-width="80%" prop="Numbers" align="center" label="编号" >
      </el-table-column>
      <el-table-column min-width="200%" prop="creatTime" align="center" label="订单生成时间">
      </el-table-column>
      <el-table-column min-width="120%" prop="serviceName" align="center" label="业务名称" >
      </el-table-column>
      <el-table-column min-width="120%" prop="orderMoney" align="center" label="订单金额" >
      </el-table-column>
      <el-table-column min-width="320%" prop="orderNumber" align="center" label="订单号"  @click="getDetail()">
      </el-table-column>
      <el-table-column min-width="120%" prop="balance" align="center" label="账户余额"  @click="getDetail()">
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
  const axios = require('axios');
  // const pageSizes = 10;
  export default {

    data() {
      return {
        // 数据
        tableData: [],
        // 分页
        currentPage1: 1, // 默认展示第一页
        pageSize: 5,
        UserId: '',
      }
    },

    methods: {
      detail(orderNumber) {
        console.log(orderNumber);
      },
      // 分页
      handleSizeChange(val) {
        console.log(`每页 ${val} 条`);
      },
      handleCurrentChange(val) {
        console.log(`当前页: ${val}`)
      },
    },
    activated() {
      this.UserId = this.$cookie.get('ek_userId')
      this.$http({
          url: this.$http.adornEkUrl('/optometrist/capitalAccountDetailList'),
          method: 'get',
          params: this.$http.adornParams({
            userId: this.UserId,
          })
        })
        .then((response) => {
          let data = response.data.data;
          let accountArray = new Array();
          for (let index = 0; index < data.length; index++) {
            if (data[index].tradeType == '-1') {
              let Numbers = index + 1; // 编号
              let creatTime = '' // 创建日期
              let serviceName = ''; // 交易类型
              let orderMoney = ''; // 订单金额
              let orderNumber = ''; // 订单号
              let balance = '' // 余额
              creatTime = data[index].createTime.split('T')[0] + ' ' + data[index].createTime.split('T')[1];
              serviceName = '提现';
              orderMoney = data[index].money.toFixed(2);
              orderNumber = data[index].orderId;
              balance = data[index].balance.toFixed(2);

              let accountJson = {
                Numbers: Numbers,
                creatTime: creatTime,
                serviceName: serviceName,
                orderMoney: orderMoney,
                orderNumber: orderNumber,
                balance: balance,
              }
              accountArray.push(accountJson)
            }

          }
          console.log(accountArray);
          this.tableData = accountArray;
        })
        .catch(function (error) {
          console.log(error);
        });
    }
  }

</script>

<style lang="scss">
  .el-table .warning-row {
    background: oldlace;
  }

  .el-table .success-row {
    background: #f0f9eb;
  }

  .pageNumber {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
  }

</style>
