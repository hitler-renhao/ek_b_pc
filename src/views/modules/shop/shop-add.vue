<template>
  <div class="shop-add">
    <!-- 流程标题 -->
    <div class="">
      <el-steps :active="active" finish-statis="success" class="top-banner">
        <el-step title="店铺入住"></el-step>
        <el-step title="完善资料"></el-step>
        <el-step title="入住成功"></el-step>
      </el-steps>
    </div>
    <!-- 店铺入住 -->
    <div v-show="active === 1">
      <el-form :model="dataForm1" :rules="dataRule1" ref="form">
        <el-form-item label="店铺名称：" prop="shopName">
          <el-col :span="18">
            <el-input v-model="dataForm1.shopName"></el-input>
          </el-col>
        </el-form-item>

        <el-form-item label="店铺地址：" required>
          <!-- 省 -->
          <el-col :span="6">
            <el-form-item prop="valueProvince">
              <el-select v-model="valueProvince" placeholder="请选择省" @change="changeProvince">
                <el-option v-for="item in provinceList" :key="item.value" :label="item.label" :value="item.value">
                </el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <!-- 市 -->
          <el-col :span="6">
            <el-form-item prop="valueCity">
              <el-select v-model="valueCity" placeholder="请选择市" @change="changeCity">
                <el-option v-for="item in cityOptions" :key="item.value" :label="item.label" :value="item.value">
                </el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <!-- 区 -->
          <el-col :span="6">
            <el-form-item prop="valueCity">
              <el-select v-model="valueOrigin" placeholder="请选择区" @change="changeOrigin">
                <el-option v-for="item in originOptions" :key="item.value" :label="item.label" :value="item.value">
                </el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-form-item>

        <el-form-item label="详细地址：" prop="shopDetailedAddr">
          <el-col :span="18">
            <el-input v-model="dataForm1.shopDetailedAddr"></el-input>
          </el-col>
        </el-form-item>

        <el-form-item label="店内电话：" prop="shopPhone">
          <el-col :span="18">
            <el-input v-model="dataForm1.shopPhone"></el-input>
          </el-col>
        </el-form-item>

        <el-form-item label="营业时间：" required>
          <el-col :span="9">
            <el-form-item prop="startTime">
              <el-time-select placeholder="起始时间" v-model="dataForm1.startTime" :picker-options="{start: '00:00',step: '00:30',end: '23:59'}">
              </el-time-select>
            </el-form-item>
          </el-col>
          <el-col :span="9">
            <el-form-item prop="endTime">
              <el-time-select placeholder="结束时间" v-model="dataForm1.endTime" :picker-options="{start: '00:00',step: '00:30',end: '23:59',minTime: startTime}">
              </el-time-select>
            </el-form-item>
          </el-col>
        </el-form-item>

        <el-form-item label="营业执照：" required>
          <div is="upload-img" :limit=1 ref="businessLicenseImgRef"></div>
        </el-form-item>

        <el-form-item>
          <el-col :span="11">
            <el-button type="primary" @click="baiduMapApi">下一步</el-button>
          </el-col>
        </el-form-item>
      </el-form>
    </div>
    <!-- 完善资料 -->
    <div v-show="active === 2">
      <el-form :model="dataForm2" :rules="dataRule2" ref="form">
        <el-form-item label="店铺主图：" required>
          <div is="upload-img" :limit=1 ref="shopMainImgRef"></div>
        </el-form-item>

        <el-form-item label="设施介绍：" prop="introduce">
          <el-col :span="18">
            <el-input type="textarea" :rows="2" placeholder="请输入详细的店铺介绍" v-model="dataForm2.introduce"></el-input>
          </el-col>
        </el-form-item>

        <el-form-item label="详情图片：" required>
          <div is="upload-img" :limit=3 ref="shopTripletImgRef" style="margin-left: 90px"></div>
        </el-form-item>

        <el-form-item label="提供服务：">
          <el-checkbox-group v-model="dataForm2.shopSkills">
            <el-checkbox v-for="item in shopSkillsList" :label="item" :key="item">{{item.title}}</el-checkbox>
          </el-checkbox-group>
        </el-form-item>

        <el-form-item label="银行卡号：" prop="bankCard">
          <el-col :span="18">
            <el-input v-model="dataForm2.bankCard"></el-input>
          </el-col>
        </el-form-item>

        <el-form-item label="账户姓名：" prop="accountName">
          <el-col :span="18">
            <el-input v-model="dataForm2.accountName"></el-input>
          </el-col>
        </el-form-item>

        <el-form-item>
          <el-col :span="11">
            <el-button type="primary" @click="shopPerfect">提交</el-button>
          </el-col>
        </el-form-item>
      </el-form>
    </div>
    <!-- 入住完成 -->
    <div v-show="active === 3">
      <el-alert title="入驻成功" type="success" description="恭喜您成功入驻e可眼睛平台！" show-icon style="font-size: 20px" v-if="buttonStatus=false">
      </el-alert>

      <el-alert title="修改成功" type="success" description="修改成功，请您注意查看！" show-icon style="font-size: 20px" v-if="buttonStatus=true">
      </el-alert>
    </div>
  </div>
</template>

<script>
  import uploadImg from '@/views/common/file/upload-img'
  import productAddOrUpdate from '@/views/modules/product/product-add-or-update'
  import axios from 'axios'
  export default {
    name: 'shop-add',
    components: {
      uploadImg,
      productAddOrUpdate
    },
    data() {
      return {
        active: 1,
        buttonStatus: false,
        provinceList: [], // 省列表
        cityList: [], // 市列表
        originList: [], // 区列表
        valueProvince: '', // 所选的省
        valueCity: '', // 所选的市
        valueOrigin: '', // 所选的区
        cityOptions: [], // 市下拉框数据
        originOptions: [], // 区下拉框数据
        baiduProvidence: '',
        baiduCity: '',
        baiduOrigin: '',
        baiduLat: '',
        baiduLng: '',
        startTime: '',
        endTime: '',
        shopSkillsList: [],
        dataForm1: {
          shopId: '',
          shopName: '',
          shopDetailedAddr: '',
          shopPhone: ''
        },
        dataForm2: {
          shopId: '',
          imgurl: '',
          introduce: '',
          shopSkills: [],
          bankCard: '',
          accountName: ''
        },
        dataRule1: {
          shopName: [{
              required: true,
              message: '店铺名称不能为空！',
              trigger: 'blur'
            },
            {
              trigger: 'blur'
            }
          ],
          valueProvince: [{
              required: true,
              message: '店铺地址不能为空！',
              trigger: 'blur'
            },
            {
              trigger: 'blur'
            }
          ],
          shopDetailedAddr: [{
              required: true,
              message: '店铺详细地址不能为空！',
              trigger: 'blur'
            },
            {
              trigger: 'blur'
            }
          ],
          shopPhone: [{
              required: true,
              message: '店铺电话不能为空！',
              trigger: 'blur'
            },
            {
              trigger: 'blur'
            }
          ],
          startTime: [{
              required: true,
              message: '店铺营业起始时间不能为空！',
              trigger: 'blur'
            },
            {
              trigger: 'blur'
            }
          ],
          endTime: [{
              required: true,
              message: '店铺营业结束时间不能为空！',
              trigger: 'blur'
            },
            {
              trigger: 'blur'
            }
          ]
        },
        dataRule2: {
          introduce: [{
              required: true,
              message: '店铺介绍不能为空！',
              trigger: 'blur'
            },
            {
              trigger: 'blur'
            }
          ],
          shopSkills: [{
              required: true,
              message: '店铺服务项目不能为空！',
              trigger: 'blur'
            },
            {
              trigger: 'blur'
            }
          ],
          bankCard: [{
              required: true,
              message: '店铺银行卡号不能为空！',
              trigger: 'blur'
            },
            {
              trigger: 'blur'
            }
          ],
          accountName: [{
              required: true,
              message: '店铺银行账户不能为空！',
              trigger: 'blur'
            },
            {
              trigger: 'blur'
            }
          ]
        }
      }
    },
    methods: {
      // 选择省
      changeProvince(val) {
        this.provinceList.forEach((province, index) => {
          if (val.toString() === this.provinceList[index].value) {
            this.cityOptions = this.provinceList[index].children
            this.valueCity = this.provinceList[index].children[0].value
            this.originOptions = this.provinceList[index].children[0].children
            this.valueOrigin = this.provinceList[index].children[0].children[0].value
          }
        })
      },
      // 选择市
      changeCity(val) {
        this.cityList.forEach((city, index) => {
          if (val.toString() === this.cityList[index].value) {
            this.originOptions = this.cityList[index].children
            this.valueOrigin = this.cityList[index].children[0].value
          }
        })
      },
      // 选择区
      changeOrigin(val) {
        this.valueOrigin = val
      },
      // 读取地址JSON文件
      _getJsonData() {
        axios.get('/static/china_address_v4.json').then((res) => {
          this.provinceList = []
          this.cityList = []
          this.originList = []
          res.data.forEach((item) => {
            if (item.value.match(/0000$/)) {
              this.provinceList.push({
                value: item.value,
                label: item.name,
                children: []
              })
            } else if (item.value.match(/00$/)) {
              this.cityList.push({
                value: item.value,
                label: item.name,
                children: []
              })
            } else {
              this.originList.push({
                value: item.value,
                label: item.name
              })
            }
          })
          for (let index in this.provinceList) {
            for (let index1 in this.cityList) {
              if (this.provinceList[index].value.slice(0, 2) === this.cityList[index1].value.slice(0, 2)) {
                this.provinceList[index].children.push(this.cityList[index1])
              }
            }
          }
          for (let item1 in this.cityList) {
            for (let item2 in this.originList) {
              if (this.originList[item2].value.slice(0, 4) === this.cityList[item1].value.slice(0, 4)) {
                this.cityList[item1].children.push(this.originList[item2])
              }
            }
          }
        })
      },
      // 店铺详情
      shopDetails: function () {
        this.$http({
            url: this.$http.adornEkUrl('/shop/queryShopByUser'),
            method: 'post',
            params: this.$http.adornParams({})
          })
          .then((data) => {
            if (data.data.code === 200) {
              // 店铺ID
              this.dataForm1.shopId = data.data.data.shop.id
              this.dataForm2.shopId = data.data.data.shop.id
              // 店铺名称
              this.dataForm1.shopName = data.data.data.shop.shopname
              // 省
              this.provinceList.forEach((province, index) => {
                if (data.data.data.shop.addresInfo.split(',')[0] === this.provinceList[index].label) {
                  this.valueProvince = this.provinceList[index].value
                }
              })
              // 市
              this.cityList.forEach((province, index) => {
                if (data.data.data.shop.addresInfo.split(',')[1] === this.cityList[index].label) {
                  this.valueCity = this.cityList[index].label
                }
              })
              // 区
              this.originList.forEach((province, index) => {
                if (data.data.data.shop.addresInfo.split(',')[2] === this.originList[index].label) {
                  this.valueOrigin = this.originList[index].label
                }
              })
              // 详细地址
              this.dataForm1.shopDetailedAddr = data.data.data.shop.addres
              // 店内电话
              this.dataForm1.shopPhone = data.data.data.shop.phone
              // 营业时间
              this.dataForm1.startTime = data.data.data.shop.businessHours.split('-')[0]
              this.dataForm1.endTime = data.data.data.shop.businessHours.split('-')[1]

              for (var i = 0; i < data.data.data.imgs.length; i++) {
                // 店铺主图
                if (data.data.data.imgs[i].type === '1') {
                  this.$refs.shopMainImgRef.result.push({
                    url: data.data.data.imgs[i].imagePath,
                    type: data.data.data.imgs[i].type
                  })
                  // 店铺环境图
                } else if (data.data.data.imgs[i].type === '2') {
                  this.$refs.shopTripletImgRef.result.push({
                    url: data.data.data.imgs[i].imagePath,
                    type: data.data.data.imgs[i].type
                  })
                  // 店铺营业执照
                } else if (data.data.data.imgs[i].type === '4') {
                  this.$refs.businessLicenseImgRef.result.push({
                    url: data.data.data.imgs[i].imagePath,
                    type: data.data.data.imgs[i].type
                  })
                }
              }
              // 店铺介绍
              this.dataForm2.introduce = data.data.data.shop.introduce
              // 提供服务
              this.dataForm2.shopSkills = JSON.parse(data.data.data.shop.shopSkills)
              console.log(this.dataForm2.shopSkills)
              // 银行卡号
              this.dataForm2.bankCard = data.data.data.bankCard.bankCard
              // 账户姓名
              this.dataForm2.accountName = data.data.data.bankCard.accountName
              // 变更按钮状态
              this.buttonStatus = true
            }
          })
      },
      // 百度地图Api
      baiduMapApi() {
        // 获取省
        this.provinceList.forEach((province, index) => {
          if (this.valueProvince === this.provinceList[index].value) {
            this.baiduProvidence = this.provinceList[index].label
          }
        })
        // 获取市
        this.cityList.forEach((province, index) => {
          if (this.valueCity === this.cityList[index].value) {
            this.baiduCity = this.cityList[index].label
          }
        })
        // 获取区
        this.originList.forEach((province, index) => {
          if (this.valueOrigin === this.originList[index].value) {
            this.baiduOrigin = this.originList[index].label
          }
        })
        // 百度地图获取经纬度
        this.$http({
          url: this.$http.adornBaiduUrl('geocoder'),
          method: 'get',
          params: this.$http.adornParams({
            'address': this.dataForm1.shopDetailedAddr,
            'output': 'json',
            'key': 'KN9omYs9VP6sdG4c2kuem8PBDjCGqgfL',
            'city': this.baiduCity
          })
        }).then(({
          data
        }) => {
          this.baiduLat = data.result.location.lat
          this.baiduLng = data.result.location.lng
          // 根据按钮
          if (this.buttonStatus === false) {
            this.shopAdd()
          } else {
            this.shopUpdat()
          }
        })
      },
      // 店铺入住
      shopAdd() {
        // 上传图片路径
        this.$http({
            url: this.$http.adornEkUrl('/shop/addShop'),
            method: 'post',
            params: this.$http.adornParams({
              addresInfo: this.baiduProvidence + ',' + this.baiduCity + ',' + this.baiduOrigin,
              addres: this.dataForm1.shopDetailedAddr,
              shopname: this.dataForm1.shopName,
              phone: this.dataForm1.shopPhone,
              businessHours: this.dataForm1.startTime + '-' + this.dataForm1.endTime,
              off: '1',
              uriImg: this.$refs.businessLicenseImgRef.result[0].url,
              longitude: this.baiduLng,
              latitude: this.baiduLat
            })
          })
          .then((data) => {
            if (data.data.code === 200) {
              // 切换标题栏
              this.active = 2
              // 获取店铺服务项目列表
              this.shopSkills()
            }
          })
      },
      // 修改店铺
      shopUpdat() {
        this.$http({
            url: this.$http.adornEkUrl('/shop/updateShop'),
            method: 'post',
            params: this.$http.adornParams({
              id: this.dataForm1.shopId,
              addresInfo: this.baiduProvidence + ',' + this.baiduCity + ',' + this.baiduOrigin,
              addres: this.dataForm1.shopDetailedAddr,
              shopname: this.dataForm1.shopName,
              phone: this.dataForm1.shopPhone,
              businessHours: this.dataForm1.startTime + '-' + this.dataForm1.endTime,
              off: '1',
              uriImg: this.$refs.businessLicenseImgRef.result[0].url,
              longitude: this.baiduLng,
              latitude: this.baiduLat
            })
          })
          .then((data) => {
            if (data.data.code === 200) {
              // 切换标题栏
              this.active = 2
              // 获取店铺服务项目列表
              this.shopSkills()
            }
          })
      },
      // 店铺服务项目列表
      shopSkills() {
        this.$http({
            url: this.$http.adornEkUrl('/shop/serviceSkillsList'),
            method: 'get',
            params: this.$http.adornParams({})
          })
          .then((data) => {
            if (data.data.code === 200) {
              alert('项目服务列表')
              data.data.data.forEach((item) => {
                this.shopSkillsList.push({
                  id: String(item.id),
                  title: item.title
                })
              })
            }
          })
      },
      // 完善店铺
      shopPerfect() {
        var imgJson = []
        let imgPic = {}
        let imgPicAry = []
        imgJson.push(this.$refs.shopMainImgRef.result[0].url)
        imgJson.push(this.$refs.shopTripletImgRef.result[0].url)
        imgJson.push(this.$refs.shopTripletImgRef.result[1].url)
        imgJson.push(this.$refs.shopTripletImgRef.result[2].url)
        for (var index = 0; index < 4; index++) {
          if (index === 0) {
            var indexx = JSON.stringify(1)
          } else {
            var indexx = JSON.stringify(2)
          }
          imgPic = {
            'type': indexx,
            'imagePath': imgJson[index]
          }
          imgPicAry.push(imgPic)
        }
        // 店铺首页形象照 // 店铺环境照 // 设备照片
        // 店铺主图
        this.dataForm2.imgurl = JSON.stringify(imgPicAry)

        // 服务项目
        console.log(this.dataForm2.shopSkills)
        this.dataForm2.shopSkills = JSON.stringify(this.dataForm2.shopSkills)

        // 店铺完善资料
        this.$http({
          url: this.$http.adornEkUrl('/shop/updateShops'),
          method: 'post',
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded'
          },
          data: this.$http.adornData(this.dataForm2, true, 'false')
        }).then(({
          data
        }) => {
          if (data && data.code === 200) {
            // 切换标题栏
            this.active = 3
            // 跳转路径
            this.$router.push({
              path: '/shop-shop-info'
            })
          } else {
            this.msg_(data.msg)
          }
        })
      }
    },
    created() {
      this.shopDetails()
      this._getJsonData()
    }
  }

</script>

<style scoped>
  .shop-add {
    margin: 0 auto;
    width: 600px;
  }

  .el-checkbox-group {
    display: flex;
    flex-direction: column;
  }

  .el-checkbox+.el-checkbox {
    margin-left: 0;
  }
  .top-banner {
    margin-bottom: 50px;
  }

</style>
