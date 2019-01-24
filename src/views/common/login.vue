<template>
	<div class="site-wrapper site-page--login">
		<div class="site-content__wrapper">
			<div class="site-content">
				<div class="brand-info">
					<h2 class="brand-info__text">ek商户管理平台</h2>
					<p class="brand-info__intro">—— 品牌成就梦想！</p>
				</div>
				<div class="login-main">
					<h3 class="login-title">商户登录</h3>
					<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" status-icon>
						<el-form-item prop="userName">
							<el-input v-model="dataForm.userName" placeholder="手机号"></el-input>
						</el-form-item>
						<el-form-item prop="password">
							<el-input v-model="dataForm.password" type="password" placeholder="密码"></el-input>
						</el-form-item>
						<el-form-item prop="captcha">
							<el-row :gutter="20">
								<el-col :span="14">
									<el-input v-model="dataForm.captcha" placeholder="验证码">
									</el-input>
								</el-col>
								<el-col :span="10" class="login-captcha">
									<img :src="captchaPath" @click="getCaptcha()" alt="">
								</el-col>
							</el-row>
						</el-form-item>
						<el-form-item>
							<el-button class="login-btn-submit" type="primary" @click="dataFormSubmit()">登录</el-button>
						</el-form-item>
					</el-form>

				</div>
				<div class="reg_forget_div">
					<el-row :gutter="20">
						<el-col :span="12">
							<el-button class="reg_forget" type="success" @click="$router.push({ name: 'register' })">注册</el-button>
						</el-col>
						<el-col :span="12">
							<el-button class="reg_forget" type="warning" @click="$router.push({ name: 'forget' })">忘记密码</el-button>
						</el-col>
					</el-row>
				</div>
			</div>

		</div>

	</div>

</template>

<script>
	import {
		getUUID
	} from '@/utils'
	import {
		isMobile,
		isPassword
	} from '@/utils/validate'

	export default {
		data() {
      var validateIsPhone = (rule, value, callback) => {
        if (!isMobile(value)) {
          callback(new Error('手机号码不正确'))
        } else {
          callback()
        }
      }
      var validateIsPassword = (rule, value, callback) => {
        if (!isPassword(value)) {
          callback(new Error('请输入6-12位的密码'))
        } else {
          callback()
        }
      }
			return {
				dataForm: {
					userName: '',
					password: '',
					uuid: '',
					captcha: ''
				},
				dataRule: {
					userName: [{
							required: true,
							message: '手机号不能为空',
							trigger: 'blur'
						},
						{
							validator: validateIsPhone,
							trigger: 'blur'
						}
					],
					password: [{
							required: true,
							message: '密码不能为空',
							trigger: 'blur'
						},
						{
							validator: validateIsPassword,
							trigger: 'blur'
						}
					],
					captcha: [{
						// required: true,
						message: '验证码不能为空',
						trigger: 'blur'
					}]
				},
				captchaPath: ''
			}
		},
		created() {
			this.getCaptcha()
		},
		methods: {
			// 提交表单
			dataFormSubmit() {
				this.$refs['dataForm'].validate((valid) => {
					if (valid) {
						this.$http({
							url: this.$http.adornUrl('/sys/login'),
							method: 'post',
							data: this.$http.adornData({
								'username': this.dataForm.userName,
								'password': this.dataForm.password,
								'uuid': this.dataForm.uuid,
								'captcha': this.dataForm.captcha
							})
						}).then(({
							data
						}) => {
							if (data && data.code === 0) {

								this.$cookie.set('token', data.token)
                this.doEklogin()
							} else {
								this.getCaptcha()
								this.$message.error(data.msg)
							}
						})
					}
				})
			},

			doEklogin: function() {
				this.$http({
					method: 'post',
					url: this.$http.adornEkUrl('/login/publicLogin'),
					params: this.$http.adornParams({
						'iphone': this.dataForm.userName,
						'password': this.dataForm.password,
					})
				}).then(({
					data
				}) => {
					if (data && data.code === 200) {

						//此处写入ek的token
						this.$cookie.set('ek_tokenKey', data.data.tokenKey)
						this.$cookie.set('ek_userId', data.data.id)
						this.$cookie.set('ek_iphone', data.data.iphone)

						console.log(this.$cookie.get('ek_tokenKey'))
						console.log(this.$cookie.get('ek_userId'))
						console.log(this.$cookie.get('ek_iphone'))
						this.$router.replace({
							name: 'home'
						})
					} else {

						// //估计是临时用的，上线的时候别忘了改，还有下面 清除人人的token的注释，也要删掉
						// console.info('登录失败 ek 公众号');
						// this.$cookie.set('ek_tokenKey','658e9e86c0a74f7380c4f6fb6f12840b1547188221264');
						// console.log(this.$cookie.get('ek_tokenKey')+'2222')

						//清除人人的token
						this.$cookie.delete('token')

						this.getCaptcha()
						this.$message.error(data.msg)
					}
				})
			},

			// 获取验证码
			getCaptcha() {
				this.dataForm.uuid = getUUID()
				this.captchaPath = this.$http.adornUrl(`/captcha.jpg?uuid=${this.dataForm.uuid}`)
			},

		}
	}
</script>

<style lang="scss">
	.site-wrapper.site-page--login {
		position: absolute;
		width: 100%;
		height: 100%;
		top: 0;
		right: 0;
		bottom: 0;
		left: 0;
		background-color: rgba(38, 50, 56, .6);
		overflow: hidden;

		&:before {
			position: fixed;
			top: 0;
			left: 0;
			z-index: -1;
			width: 100%;
			height: 100%;
			content: "";
			background-size: 76% 100%;
			background-repeat: no-repeat;
			background-image: url(~@/assets/img/login_bg.jpg);
		}

		.site-content__wrapper {
			position: absolute;
			top: 0;
			right: 0;
			bottom: 0;
			left: 0;
			padding: 0;
			margin: 0;
			overflow-x: hidden;
			overflow-y: auto;
			background-color: transparent;
		}

		.site-content {
			min-height: 100%;
			padding: 30px 500px 30px 30px;
		}

		.brand-info {
			margin: 0 auto;
			width: 400px;
			height: 105px;
			position: relative;
			margin-top: 200px;
			color: #fff;
		}

		.brand-info__text {
			margin: 0 0 22px 0;
			font-size: 48px;
			font-weight: 400;
			/*text-transform: uppercase;*/
		}

		.brand-info__intro {
			margin: 0 10px 100px 200px;
			font-size: 16px;
			line-height: 1.58;
			/*opacity: .6;*/
		}

		.login-main {
			position: absolute;
			top: 0;
			right: 0;
			padding: 150px 60px 180px;
			width: 470px;
			min-height: 100%;
			background-color: #fff;
		}

		.login-title {
			font-size: 16px;
		}

		.login-captcha {
			overflow: hidden;

			>img {
				width: 100%;
				cursor: pointer;
			}
		}

		.login-btn-submit {
			width: 100%;
			margin-top: 38px;
		}

		.reg_forget_div {
			position: absolute;
			top: 480px;
			right: 0;
			padding: 0px 60px 0px;
			width: 470px;
			background-color: #fff;
		}

		.reg_forget {
			width: 100%;
		}
	}
</style>
