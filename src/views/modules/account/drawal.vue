<template>
  <div class="drawal">
    <el-row>
      <el-col :span="24">
        <div class="grid-content bg-purple-dark">
          <span>提现到银行卡</span>
          <span class="totalMoney">可提现金额 : <span class="totalNumber">{{ Balance }}</span>元</span>
          <span class="drawalRecord" @click="drawalRecord">提现记录</span>
        </div>
      </el-col>
    </el-row>

    <!-- 表单 -->
    <el-form ref="form" label-width="80px">
      <el-form-item label="提现账户">
        <div class="cardBank">
          <span>{{ Bank }}</span>
          <span>尾号: <i>{{ BankCardNumber }}</i> </span>
        </div>
      </el-form-item>
      <el-form-item label="提现金额">
        <el-input v-model="MuchMoney"></el-input>
      </el-form-item>
      <el-form-item label="到账时间">
        <el-radio-group>
          <el-radio label="1-3天到账"></el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">立即提现</el-button>
      </el-form-item>
    </el-form>

    <!-- 提示 -->
    <el-alert style="text-align: center; font-size: 12px" title="e可平台提现协议" type="success" :closable="false">
      <p align="left">1.商家可直接在e可平台进行提现，提现时需按步骤填写姓名、银行卡号、开户行名称、手机号、提现金额等信息，e可现只提供银行卡提现这一种提现服务；</p>
      <p align="left">2.商家每天可提现1次，提现金额不得小于100，提现申请提交成功后，e可会在1 - 7个工作日内完成打款；</p>
      <p align="left">3.提现处理时间为每周一至周五9:00 - 18:00，如遇周六、周日、国家法定节假日商家的提现处理工作将会顺延至假日后的第一个工作日。</p>
    </el-alert>
  </div>
</template>

<script>
  const axios = require('axios');
  import Utils from '@/utils/jump'

  export default {
    data() {
      return {
        UserId: '',
        Balance: '',
        Bank: '',
        BankCardNumber: '',
        MuchMoney: '',
      }
    },
    methods: {

      // 跳转提现记录
      drawalRecord() {
        this.$router.push({
          path: '/account-drawal-record'
        })
      },

      // 提现
      onSubmit() {
        console.log(this.MuchMoney);
        this.$http({
            url: this.$http.adornEkUrl('/optometrist/userFinanceWithdraw'),
            method: 'post',
            params: this.$http.adornParams({
              userId: this.UserId,
              money: this.MuchMoney,
            })
          })
          .then((response) => {
            // 余额
            this.getMoney();
            this.drawalRecord()
          })
          .catch(function (error) {
            console.log(error);
          });
      },

      // 余额
      getMoney() {
        this.$http({
            url: this.$http.adornEkUrl('/optometrist/userCapitalAccount'),
            method: 'get',
            params: this.$http.adornParams({
              userId: this.UserId,
            })
          })
          .then((response) => {
            console.log(response);
            this.Balance = response.data.data.money.toFixed(2)
          })
          .catch(function (error) {
            console.log(error);
          });
      },

      // 银行卡
      getBankCard() {
        this.$http({
            url: this.$http.adornEkUrl('/optometrist/userBankCard'),
            method: 'get',
            params: this.$http.adornParams({
              userId: this.UserId,
            })
          })
          .then((response) => {
            if (response.data.code == 200) {
              console.log(response);
              this.Bank = response.data.data.openingBank
              this.BankCardNumber = response.data.data.bankCard
              } else if (response.data.code == 400 && response.data.msg == '未绑定银行卡!') {
            // } else if (response.data.code == 200) {
              Utils.$emit('closeTab', 'msg');
              this.$alert('您当前的账户未绑定银行卡, 无法使用提现功能, 请移步完善银行卡信息页面进行完善银行卡信息!', '温馨提示', {
                confirmButtonText: '确定',
                callback: action => {
                  this.$router.push({ // 跳转回订单列表页
                    path: '/shop-shop-add'
                  })
                }
              });

            }

          })
          .catch(function (error) {
            console.log(error);
          });
      }
    },

    created() {
      this.UserId = this.$cookie.get('ek_userId')
      this.getMoney();
      this.getBankCard();
    },
    
  }

</script>

<style lang="scss">
  .drawalRecord {
    position: absolute;
    right: 20px;
    color: #fff;
    font-size: 16px;
    line-height: 40px;
    border: none;
  }

  .el-row {
    margin-bottom: 20px;

    &:last-child {
      margin-bottom: 0;
    }
  }

  .el-col {
    border-radius: 4px;
  }

  .bg-purple-dark {
    background: #99a9bf;
  }

  .bg-purple {
    background: #d3dce6;
  }

  .bg-purple-light {
    background: #e5e9f2;
  }

  .grid-content {
    border-radius: 4px;
    min-height: 36px;
    line-height: 36px;
    color: #fff;
    font-size: 20px;
    padding-left: 50px;
  }

  .grid-content .totalMoney {
    font-size: 14px;
    padding-left: 30px;
  }

  .totalNumber {
    font-size: 30px;
    color: #FC493A;
  }

  .row-bg {
    padding: 10px 0;
    background-color: #f9fafc;
  }


  // 到账时间
  .el-radio__inner {
    background-color: #17B3A3;
    border-color: #17B3A3;
  }

  .el-radio__inner::after {
    background-color: #fff;
    transform: translate(-50%, -50%) scale(1);
  }

  // 提现账户
  .cardBank {
    -webkit-appearance: none;
    background-color: #fff;
    background-image: none;
    border-radius: 4px;
    border: 1px solid #dcdfe6;
    -webkit-box-sizing: border-box;
    box-sizing: border-box;
    color: #606266;
    display: inline-block;
    font-size: inherit;
    height: 40px;
    line-height: 40px;
    outline: 0;
    padding: 0 15px;
    -webkit-transition: border-color .2s cubic-bezier(.645, .045, .355, 1);
    transition: border-color .2s cubic-bezier(.645, .045, .355, 1);
    width: 100%;
  }

  .cardBank span:last-child {
    padding-left: 20px;

    i {
      font-size: 18px;
      font-style: normal;
    }
  }

</style>
