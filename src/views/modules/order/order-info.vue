<template>
  <div class="order-info">
    <el-steps :active="active" align-center>
      <el-step title="待接单"></el-step>
      <el-step title="待发货"></el-step>
      <el-step title="待收货"></el-step>
      <el-step title="待评价"></el-step>
      <el-step title="订单完成"></el-step>
    </el-steps>

    <body>
      <div class="title">
        <p class="title-left fl">
          <i class="el-icon-warning"></i>
          <span>当前订单状态: <b>{{ orderStatus }}</b></span>
        </p>
        <ul class="fr title-right">
          <li class="fl" @click="orderReceiving">接单</li>
          <li class="fl" @click="sendProduct = true">订单发货</li>
          <li class="fl" @click="cancelOrder = true">取消订单</li>
        </ul>
      </div>

      <div class="basicInfo information">
        <i class="el-icon-menu"> 基本信息</i>
        <el-table :data="basicInfo1" border style="width: 100%">
          <el-table-column prop="amount1" label="订单编号">
          </el-table-column>
          <el-table-column prop="amount2" label="用户账号">
          </el-table-column>
          <el-table-column prop="amount3" label="支付方式">
          </el-table-column>
          <el-table-column prop="amount4" label="订单来源">
          </el-table-column>
        </el-table>
        <el-table :data="basicInfo2" border style="width: 100%">
          <el-table-column prop="amount1" label="订单类型">
          </el-table-column>
          <el-table-column prop="amount2" label="配送方式">
          </el-table-column>
          <el-table-column prop="amount3" label="物流单号">
          </el-table-column>
          <el-table-column prop="amount4" label="订单可得积分">
          </el-table-column>
        </el-table>
      </div>

      <div class="consigneeInfo information">
        <i class="el-icon-menu"> 收货人信息</i>
        <el-table :data="consigneeInfo" border style="width: 100%">
          <el-table-column prop="amount1" label="收货人">
          </el-table-column>
          <el-table-column prop="amount2" label="手机号码">
          </el-table-column>
          <el-table-column prop="amount3" label="收货地址">
          </el-table-column>
        </el-table>
      </div>

      <div class="productInfo information">
        <i class="el-icon-menu"> 商品信息</i>
        <el-table :data="productInfo" border show-summary style="width: 100%">
          <el-table-column prop="amount1" label="商品图片" sortable width="120">
            <template slot-scope="scope">
              <img :src="scope.row.amount1" width="100" class="head_pic" />
            </template>
          </el-table-column>
          <el-table-column prop="amount2" label="商品名称">
          </el-table-column>
          <el-table-column prop="amount3" label="规格">
          </el-table-column>
          <el-table-column prop="amount4" label="数量">
          </el-table-column>
          <el-table-column prop="amount5" label="小计">
          </el-table-column>
        </el-table>
      </div>
    </body>

    <!-- 取消订单 -->
    <el-dialog title="取消订单" :visible.sync="cancelOrder">
      <el-form>
        <el-form-item label="取消备注" :label-width="formLabelWidth">
          <el-input v-model="cancels" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="cancelOrder = false">取 消</el-button>
        <el-button type="primary" @click="cancel">确 定</el-button>
      </div>
    </el-dialog>

    <!-- 订单发货 -->
    <el-dialog title="订单发货" :visible.sync="sendProduct">
      <el-form>
        <el-form-item label="发货快递" for :label-width="formLabelWidth">
          <el-select v-model="value" placeholder="快递公司">
            <el-option v-for="(item, index) in options" :key="item.value" :label="item.label" :value="item.value">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="订单发货" :label-width="formLabelWidth">
          <el-input v-model="send" autocomplete="off" type="number" class="codeNumber"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="sendProduct = false">取 消</el-button>
        <el-button type="primary" @click="sends">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
  import Utils from '@/utils/jump'

  export default {
    data() {
      return {
        // 订单状态
        orderStatus: '待收货',

        // 取消订单
        cancelOrder: false,
        sendProduct: false,
        cancels: '',
        send: '',

        // 订单发货
        options: [{
          value: '顺丰',
          label: '顺丰'
        }, {
          value: '圆通',
          label: '圆通'
        }, {
          value: '申通',
          label: '申通'
        }, {
          value: '中通',
          label: '中通'
        }, {
          value: '韵达',
          label: '韵达'
        }, {
          value: 'EMS',
          label: 'EMS'
        }, {
          value: '邮政',
          label: '邮政'
        }, {
          value: '天天',
          label: '天天'
        }, {
          value: '百世',
          label: '百世'
        }],
        value: '',
        formLabelWidth: '80px',

        // 基本信息
        active: 0,
        title: 0,
        basicInfo1: [{
          amount1: 'order.id', // 订单编号
          amount2: 'receiverName', // 用户账号
          amount3: '微信支付', // 支付方式
          amount4: '微信', //订单来源
        }],
        basicInfo2: [{
          amount1: '普通订单', // 订单类型
          amount2: 'obtainMode = 0 快递', // 配送方式
          amount3: 'waybillNumber', //物流单号
          amount4: 'orderTotalPrice * 基数', // 订单可得积分
        }],

        // 收货人信息
        consigneeInfo: [{
          amount1: '普通订单', // 收货人
          amount2: 'obtainMode = 0 快递', // 手机号码
          amount3: 'waybillNumber', //收货地址
        }],

        // 商品信息
        productInfo: [{
          amount1: '普通订单', // 商品图片
          amount2: 'obtainMode = 0 快递', // 商品名称
          amount3: 'waybillNumber', //规格
          amount4: 'waybillNumber', //数量
          amount5: 'waybillNumber', //小计
        }]
      }
    },
    created() {
      // alert(this.$route.query.id)
      this.title = this.$route.query.id
      console.log(this.title);

      // 未传ID, 不是由订单列表页跳转, 拦截
      if (!this.title) {
        this.$message.error('请由订单列表页点击 "查看" 进入订单详情');
        Utils.$emit('closeTab', 'msg'); // 触发closeTab事件, 接收msg数据, msg数据就是关闭当前标签页方法
        this.$router.push({ // 跳转回订单列表页
          path: '/order-order'
        })
      } else {
        this.basicInfo(this.$route.query.id)
      }
      // this.baidu
    },
    methods: {

      // 基本信息
      basicInfo(productOrderId) {
        this.$http({
            url: this.$http.adornEkUrl('/ekProductOrder/productOrderInfo'),
            method: 'post',
            params: this.$http.adornParams({
              productOrderId: productOrderId,
            })
          })
          .then((response) => {
            let data = response.data.data;
            // 订单状态
            switch (data.order.orderStatus) {
              case '3':
                this.active = 1;
                this.orderStatus = '待接单';
                break;
              case '4':
                this.active = 2;
                this.orderStatus = '待发货';
                break;
              case '5':
                this.active = 3;
                this.orderStatus = '待收货';
                break;
              case '6':
                this.active = 4;
                this.orderStatus = '待评价';
                break;
              case '1':
                this.active = 5;
                this.orderStatus = '已完成';
                break;
              case '0':
              case '-1':
              case '-2':
                this.active = -1;
                this.orderStatus = '订单取消';
                break;
            }
            // 基本信息
            this.basicInfo1[0].amount1 = data.order.id;
            this.basicInfo1[0].amount2 = data.order.buyUserPhone;
            if (data.order.obtainMode == '0') {
              this.basicInfo2[0].amount2 = '快递';
            } else {
              this.basicInfo2[0].amount2 = '自提';
            }
            this.basicInfo2[0].amount3 = data.order.waybillNumber;
            this.basicInfo2[0].amount4 = data.order.orderTotalPrice * 10;

            // 收货人信息
            this.consigneeInfo[0].amount1 = JSON.parse(data.order.addrJson).receiverName
            this.consigneeInfo[0].amount2 = JSON.parse(data.order.addrJson).receiverPhone
            this.consigneeInfo[0].amount3 = JSON.parse(data.order.addrJson).receiverAddress

            // 商品信息
            console.log(JSON.parse(data.order.productJson));
            let productJson = JSON.parse(data.order.productJson);

            for (let index = 0; index < productJson.length; index++) {
              console.log(index);
              this.productInfo[index].amount1 = productJson[index].imgUrl
              this.productInfo[index].amount2 = productJson[index].productName
              this.productInfo[index].amount3 = productJson[index].spectName
              this.productInfo[index].amount4 = productJson[index].count
              this.productInfo[index].amount5 = productJson[index].totalPrice
            }
          })
          .catch(function (error) {
            console.log(error);
          });
      },

      // 取消订单
      cancel() {
        if (!this.cancels) {
          this.$message.error('请填写取消订单原因!!!');
        } else {
          this.cancelOrder = false;
          this.$http({
              url: this.$http.adornEkUrl('/ekProductOrder/cancelOrder'),
              method: 'post',
              params: this.$http.adornParams({
                orderId: this.$route.query.id,
                orderRemark: this.$el.querySelector("input").value
              })
            })
            .then((response) => {
              if (response.data.code == 200) {
                this.orderStatus = '订单取消';
                this.active = -1;
                this.$message({
                  message: '订单取消成功',
                  type: 'success'
                });
              } else if (response.data.code == 400) {
                this.$message.error('取消失败, 当前状态不能取消订单!');
              }
            })
        }
      },

      // 订单发货
      sends() {
        if (!this.value) {
          this.$message.error('发货失败, 请选择快递公司!');
        } else if (!this.send) {
          this.$message.error('发货失败, 请填写快递单号!');
        } else {
          this.sendProduct = false;
          this.$http({
              url: this.$http.adornEkUrl('/ekProductOrder/sendGoods'),
              method: 'post',
              params: this.$http.adornParams({
                expressName: this.value, // 物流公司
                waybillNumber: this.send, // 运单号
                productOrderId: this.$route.query.id // 订单id
              })
            })
            .then((response) => {
              this.orderStatus = '待收货'
              this.active = 3;
              if (response.data.code == 200) {
                this.$message({
                  message: '订单发货成功',
                  type: 'success'
                });
              } else if (response.data.code == 400) {
                this.$message.error('发货失败, 当前状态不能发货!');
              }
            })
        }
      },

      // 接单
      orderReceiving() {
        this.$http({
            url: this.$http.adornEkUrl('/ekProductOrder/updateOrderState'),
            method: 'post',
            params: this.$http.adornParams({
              state: 4, // 订单号
              productOrderId: this.$route.query.id // 订单id
            })
          })
          .then((response) => {
            if (response.data.code == 200) {
              this.orderStatus = '待发货'
              this.active = 2;
              this.$message({
                message: '接单成功',
                type: 'success'
              });
            } else if (response.data.code == 400) {
              this.$message.error('接单失败, 当前状态不能接单!');
            }
          })
      }
    },

  }

</script>

<style scoped>
  .fr {
    float: right;
  }

  .fl {
    float: left;
  }

  li {
    list-style: none;
  }

  body {
    margin-top: 30px;
  }

  .title {
    height: 80px;
    background-color: #F3F3F3;
    border: 1px solid #ccc
  }

  .title-left {
    color: red;
    line-height: 80px;
    margin: 0;
    padding-left: 20px;
    font-size: 16px;
    width: 250px;
  }

  .title-right {
    margin-top: 20px;
  }

  .title-right li {
    border: 1px solid #ccc;
    padding: 5px;
    background-color: #fff;
    margin: 5px;
  }

  .el-icon-menu,
  .el-icon-menu:before,
  .el-icon-menu:after {
    font-size: 20px;
    line-height: 50px;
  }

  .information {
    margin-top: 50px;
  }

</style>
