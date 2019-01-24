<template>
  <div class="account-details">
    <el-table :data="tableData" border style="width: 100%">
      <el-table-column min-width="40%" prop="Numbers" label="编号"  align="center">
      </el-table-column>
      <el-table-column min-width="80%" prop="creatTime" label="订单生成时间"  align="center">
      </el-table-column>
      <el-table-column min-width="40%" prop="PaymentType" label="收支类型" align="center">
      </el-table-column>
      <el-table-column min-width="40%" prop="serviceName" label="业务名称" align="center">
      </el-table-column>
      <el-table-column min-width="60%" prop="orderMoney" label="订单金额"  align="center">
      </el-table-column>
      <el-table-column min-width="150%" prop="orderNumber" label="订单号" align="center">
      </el-table-column>
      <el-table-column min-width="80%" prop="balance" label="账户余额" align="center">
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
  const axios = require('axios');
  const pageSizes = 10;
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

    methods: {},

    activated () {
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
            let Numbers = index + 1; // 编号
            let creatTime = '' // 创建日期
            let PaymentType = ''; // 收支类型
            let serviceName = ''; // 交易类型
            let orderMoney = ''; // 订单金额
            let orderNumber = ''; // 订单号
            let balance = '' // 余额
            creatTime = data[index].createTime.split('T')[0] + ' ' + data[index].createTime.split('T')[1];
            switch (data[index].budgetType) {
              case '0':
                PaymentType = '收入';
                break;
              case '1':
                PaymentType = '支出';
                break;
            };
            switch (data[index].tradeType) {
              case '-2':
                serviceName = '退款';
                break;
              case '-1':
                serviceName = '提现';
                break;
              case '1':
                serviceName = '订单';
                break;
            }
            orderMoney = data[index].money;
            orderNumber = data[index].orderId;
            balance = data[index].balance;

            let accountJson = {
              Numbers: Numbers,
              creatTime: creatTime,
              PaymentType: PaymentType,
              serviceName: serviceName,
              orderMoney: orderMoney.toFixed(2) + '元',
              orderNumber: orderNumber,
              balance: balance.toFixed(2) + '元',
            }
            accountArray.push(accountJson)
          }
          console.log(accountArray);
          this.tableData = accountArray;
          this.pageSize = Math.ceil(data.length / pageSizes);
          console.log(this.pageSize);
        })
        .catch(function (error) {
          console.log(error);
        });
    },
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
