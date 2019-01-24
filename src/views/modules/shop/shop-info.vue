<template>
  <div id="shopInfo">
    <i class="el-icon-menu"> 基本信息</i>
    <el-button type="primary" plain style="float: right; margin-bottom: 20px;" @click="editShopInfo">店铺信息修改</el-button>
    <!-- <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="info" label="店铺" width="180">
      </el-table-column>
      <el-table-column prop="details" label="信息">
      </el-table-column>
    </el-table> -->

    <!-- <div class="infor" v-for="item in tableData" :key="item">
      <i class="el-icon-menu"> {{ item.info }}</i>
      <p>{{ item.details }}</p>
    </div> -->

    <div style="clear:both"></div>

    <table cellspacing="0">
      <tbody>
        <tr>
          <td> {{ tableData[0].info }} </td>
          <td> {{ tableData[0].details }} </td>
        </tr>
        <tr>
          <td> {{ tableData[1].info }} </td>
          <td> {{ tableData[1].details }} </td>
        </tr>
        <tr>
          <td> {{ tableData[2].info }} </td>
          <td> {{ tableData[2].details }} </td>
        </tr>
        <tr>
          <td> {{ tableData[3].info }} </td>
          <td> {{ tableData[3].details }} </td>
        </tr>
      </tbody>
    </table>

    <div class="shopLogo">
      <i class="el-icon-menu"> 店铺logo</i>
      <img :src="shopLogo" alt="" width="300" class="shopLogoPic">
    </div>

    <div class="shopLogo">
      <i class="el-icon-menu"> 营业执照</i>
      <img :src="zhizhao" alt="" width="300" class="shopLogoPic">
    </div>
    <div class="shopDetail">
      <i class="el-icon-menu shopDetailText"> 店铺详情图</i>
      <img :src="item" alt="" width="300" class="shopDetailPic" v-for="item in shopDetail" :key="item">
    </div>


  </div>
</template>

<script>
  import Utils from '@/utils/jump'

  export default {
    data() {
      return {
        tableData: [{
          info: '店铺名',
          details: ''
        }, {
          info: '店铺地址',
          details: ''
        }, {
          info: '营业时间',
          details: ''
        }, {
          info: '服务到期时间',
          details: ''
        }],
        address: [],

        // 店铺主图
        // 商品信息
        shopLogo: '123.png',
        // 营业执照
        zhizhao: '123.png',

        // 店铺详情图
        shopDetail: [],

        // 店铺id
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

            this.tableData[0].details = response.data.data.shop.shopname;

            this.address = response.data.data.shop.addresInfo;

            this.tableData[1].details = this.address.split(',')[0] + this.address.split(',')[1] + this.address.split(',')[2] + response.data.data.shop.addres;

            this.tableData[2].details = response.data.data.shop.businessHours;

            let endTime1 = response.data.data.shop.createTime.split('T')[0]
            let endTime2 = response.data.data.shop.createTime.split('T')[1].split('.')[0]
            let endTime3 = endTime1 + ' ' + endTime2;
            let endTime4 = Number(endTime3.split('-')[0]) + 1;
            let endTime = endTime4 + endTime3.slice(4, endTime3.length);
            this.tableData[3].details = endTime;

            for (var index = 0, item = 0; index < response.data.data.imgs.length; index++) {
              if (response.data.data.imgs[index].type == '1') {
                this.shopLogo = response.data.data.imgs[index].imagePath;
              } else if (response.data.data.imgs[index].type == '4') {
                this.zhizhao = response.data.data.imgs[index].imagePath;
              } else {
                this.shopDetail[item] = response.data.data.imgs[index].imagePath;
                item++;
              }
            }

            this.shopId = response.data.data.shop.id;
            
          } else if (response.data.code == 400 && response.data.msg == '没有数据') {
            Utils.$emit('closeTab', 'msg');
            this.$router.push({
              path: '/shop-shop-add'
            })
          }
        })
    },

    methods: {
      editShopInfo() {
        this.$router.push({
          path: '/shop-shop-add',
          query: {
            id: this.shopId
          }
        })
      }
    },
  }

</script>

<style scoped>
  #shopInfo {
    margin-left: 30px;
  }

  .el-icon-menu {
    line-height: 50px;
    margin-top: 10px;
    font-size: 16px;
  }

  .shopLogoPic {
    display: block;
    border: 1px solid #ccc;
  }

  .shopDetailText {
    display: block;
    margin-top: 50px;
  }

  >>>td {
    padding: 5px 20px;
    /* border: .5px solid #ccc; */
  }

</style>
