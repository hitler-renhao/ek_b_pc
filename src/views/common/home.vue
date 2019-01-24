<template>
  <div class="mod-home">
    <el-row>
      <el-col :span="24">
        <div class="grid-content bg-purple-dark">待处理事务</div>
      </el-col>
    </el-row>

    <div class="notice">
      <el-badge :value="Pending.Sending" class="item">
        <el-button size="small">待发货订单</el-button>
      </el-badge>
      <el-badge :value="Pending.Refund" class="item">
        <el-button size="small">待处理退款</el-button>
      </el-badge>
      <el-badge :value="Pending.SalesReturn" class="item" type="primary">
        <el-button size="small">待处理退货订单</el-button>
      </el-badge>
      <el-badge :value="Pending.CheckFail" class="item" type="warning">
        <el-button size="small">商品审核失败</el-button>
      </el-badge>
      <el-badge :value="Pending.Serves" class="item" type="warning">
        <el-button size="small">服务到期续费</el-button>
      </el-badge>
      <el-badge :value="Pending.Advertising" class="item" type="warning">
        <el-button size="small">广告位即将到期</el-button>
      </el-badge>
    </div>

    <!-- 今日订单 -->
    <div class="todayOrder">
      <el-row>
        <el-col :span="24">
          <div class="grid-content bg-purple-dark">今日订单</div>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6">
          <div class="grid-content today">
            <i class="el-icon-tickets"></i>
            <div class="account">
              <p>今日订单总数</p>
              <p>{{ TodayOrder.TodayOrders }}</p>
            </div>
            <div style="clear:both;"></div>
          </div>
        </el-col>
        <el-col :span="6">
          <div class="grid-content today">
            <i class="el-icon-tickets"></i>
            <div class="account">
              <p>今日销售总额</p>
              <p>¥ {{ TodayOrder.TodayCounts }}</p>
            </div>
            <div style="clear:both;"></div>
          </div>
        </el-col>
        <el-col :span="6">
          <div class="grid-content today">
            <i class="el-icon-tickets"></i>
            <div class="account">
              <p>昨日销售总额</p>
              <p>¥ {{ TodayOrder.YesCounts }}</p>
            </div>
            <div style="clear:both;"></div>
          </div>
        </el-col>
        <el-col :span="6">
          <div class="grid-content today">
            <i class="el-icon-tickets"></i>
            <div class="account">
              <p>近7天销售总额</p>
              <p>¥ {{ TodayOrder.WeekCounts }}</p>
            </div>
            <div style="clear:both;"></div>
          </div>
        </el-col>
      </el-row>
    </div>

    <!-- 商品总览 -->
    <div class="todayOrder">
      <el-row>
        <el-col :span="24">
          <div class="grid-content bg-purple-dark">商品总览</div>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6">
          <div class="grid-content today">
            <i class="el-icon-tickets"></i>
            <div class="account">
              <p>已下架数</p>
              <p>{{ Products.Off }}</p>
            </div>
            <div style="clear:both;"></div>
          </div>
        </el-col>
        <el-col :span="6">
          <div class="grid-content today">
            <i class="el-icon-tickets"></i>
            <div class="account">
              <p>已上架数</p>
              <p>{{ Products.On }}</p>
            </div>
            <div style="clear:both;"></div>
          </div>
        </el-col>
        <el-col :span="6">
          <div class="grid-content today">
            <i class="el-icon-tickets"></i>
            <div class="account">
              <p>库存紧张数</p>
              <p>{{ Products.Repertory }}</p>
            </div>
            <div style="clear:both;"></div>
          </div>
        </el-col>
        <el-col :span="6">
          <div class="grid-content today">
            <i class="el-icon-tickets"></i>
            <div class="account">
              <p>全部商品数</p>
              <p>{{ Products.All }}</p>
            </div>
            <div style="clear:both;"></div>
          </div>
        </el-col>
      </el-row>
    </div>

    <!-- 用户总览 -->
    <!-- <div class="todayOrder">
      <el-row>
        <el-col :span="24">
          <div class="grid-content bg-purple-dark">用户总览</div>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6">
          <div class="grid-content today">
            <i class="el-icon-tickets"></i>
            <div class="account">
              <p>今日新增</p>
              <p>{{ Users.TodayNew }}</p>
            </div>
            <div style="clear:both;"></div>
          </div>
        </el-col>
        <el-col :span="6" :push="3">
          <div class="grid-content today">
            <i class="el-icon-tickets"></i>
            <div class="account">
              <p>昨日新增</p>
              <p>{{ Users.YesNew }}</p>
            </div>
            <div style="clear:both;"></div>
          </div>
        </el-col>
        <el-col :span="6" :push="5">
          <div class="grid-content today">
            <i class="el-icon-tickets"></i>
            <div class="account">
              <p>本月新增</p>
              <p>{{ Users.MonthNew }}</p>
            </div>
            <div style="clear:both;"></div>
          </div>
        </el-col>
        <el-col :span="6" :push="8">
          <div class="grid-content today">
            <i class="el-icon-tickets"></i>
            <div class="account">
              <p>会员总数</p>
              <p>{{ Users.VipCounts }}</p>
            </div>
            <div style="clear:both;"></div>
          </div>
        </el-col>
      </el-row>
    </div> -->

  </div>
</template>

<script>
  export default {
    data() {
      return {
        // 待处理事务
        Pending: {
          Sending: '',
          Refund: '',
          SalesReturn: '',
          CheckFail: '',
          Serves: '',
          Advertising: '',
        },
        // 今日订单
        TodayOrder: {
          TodayOrders: '',
          TodayCounts: '',
          YesCounts: '',
          WeekCounts: '',
        },
        // 商品总览
        Products: {
          Off: '',
          On: '',
          Repertory: '',
          All: ''
        },
        // 用户总览
        Users: {
          TodayNew: '',
          YesNew: '',
          MonthNew: '',
          VipCounts: '',
        },
        shopId: '',
      }
    },
    created() {
      this.$http({
          url: this.$http.adornEkUrl('/shop/queryShopByUser'),
          method: 'post',
          params: this.$http.adornParams({})
        })
        .then((response) => {
          if (response.data.code == 200) {
            this.shopId = response.data.data.shop.id
            this.$http({
                url: this.$http.adornEkUrl('/index/pu/queryIndexJson'),
                method: 'post',
                params: this.$http.adornParams({
                  shopId: this.shopId
                })
              })
              .then((response) => {
                if (response.data.code == 200) {
                  let data = response.data.data
                  this.Products.All = data[0].value_
                  this.Products.Repertory = data[1].value_
                  this.Products.Off = data[2].value_
                  this.Products.On = data[3].value_

                  this.TodayOrder.TodayOrders = data[4].value_
                  this.TodayOrder.TodayCounts = data[5].value_
                  this.TodayOrder.YesCounts = data[6].value_
                  this.TodayOrder.WeekCounts = data[7].value_

                  this.Pending.Sending = data[8].value_
                  this.Pending.Refund = data[9].value_
                  this.Pending.SalesReturn = data[10].value_
                }
              })
          }
        })
    },
  }

</script>

<style scoped>
  >>>.bg-purple-dark {
    background: #99a9bf;
  }

  >>>.bg-purple {
    background: #d3dce6;
  }

  >>>.bg-purple-light {
    background: #e5e9f2;
  }

  >>>.grid-content {
    /* border-radius: 10px; */
    border-top-left-radius: 10px;
    border-top-right-radius: 10px;
    min-height: 36px;
    color: #fff;
    font-size: 16px;
    line-height: 36px;
    padding-left: 10px;
  }

  >>>.row-bg {
    padding: 10px 0;
    background-color: #f9fafc;
  }


  >>>.notice {
    border: 1px solid #ccc;
    border-top: none;
    padding: 20px 50px;
    display: flex;
    justify-content: space-between;
    border-bottom-left-radius: 10px;
    border-bottom-right-radius: 10px;
  }

  >>>.item {
    margin-top: 10px;
    margin-right: 40px;
  }

  >>>.todayOrder {
    margin-top: 50px;
  }

  >>>.todayOrder .grid-content {
    border-radius: 10px;
    min-height: 36px;
    color: #fff;
    font-size: 16px;
    line-height: 36px;
    padding-left: 10px;
    margin-top: 20px;
  }

  >>>.today {
    border: 1px solid #ccc;
    color: #1ABC9C !important;
    font-size: 60px !important;
  }

  >>>.account {
    float: right;
    margin-right: 30px;
    font-size: 18px;
    line-height: 14px;
    color: #666;
  }

  >>>.el-icon-tickets {
    padding-top: 10px;
  }

</style>
