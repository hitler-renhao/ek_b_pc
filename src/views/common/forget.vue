<template>
	<div class="site-wrapper site-page--login">
		<div class="site-content__wrapper">
			<div class="site-content">
				<div class="brand-info">
					<h2 class="brand-info__text">ek商户管理平台</h2>
					<p class="brand-info__intro">—— 品牌成就梦想！</p>
				</div>
				<div class="login-main">
					<h3 class="login-title">找回密码</h3>
					<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" status-icon>
						<el-form-item prop="userName">
							<el-input v-model="dataForm.userName" placeholder="手机号"></el-input>
						</el-form-item>
						<el-form-item prop="password">
							<el-input v-model="dataForm.password" type="password" placeholder="新密码"></el-input>
						</el-form-item>
						<el-form-item prop="password2">
							<el-input v-model="dataForm.password2" type="password" placeholder="请再次输入新密码"></el-input>
						</el-form-item>
						<el-form-item>
							<el-row :gutter="20">
								<el-col :span="14">
									<el-input v-model="dataForm.captcha" @input="checkCaptcha()" placeholder="验证码">
									</el-input>
								</el-col>
								<el-col :span="10" class="login-captcha">
									<img :src="captchaPath" @click="getCaptcha()" alt="">
								</el-col>
							</el-row>
						</el-form-item>
						<el-form-item prop="sms">
							<el-row :gutter="20">
								<el-col :span="14">
									<el-input v-model="dataForm.sms" placeholder="短信验证码">
									</el-input>
								</el-col>
								<el-col :span="10" class="login-captcha">
									<el-button class="get-sms" :disabled="dataForm.flag" type="warning" @click="getSms()">{{dataForm.buttonContent}}</el-button>
								</el-col>
							</el-row>
						</el-form-item>
						<el-form-item>
							<el-button class="login-btn-submit" type="primary" @click="dataFormSubmit()">提交</el-button>
						</el-form-item>
					</el-form>

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
			var validateConfirmPassword = (rule, value, callback) => {

				if (this.dataForm.password !== value) {
					callback(new Error('两次输入的密码不一致'))
				} else {
					callback()
				}
			}
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
					password2: '',
					uuid: '',
					captcha: '',
					sms: '',
					flag: '',
					switcher: '',
					second: '',
					timer: null,
					buttonContent: ''
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
					password2: [{
							required: true,
							message: '密码不能为空',
							trigger: 'blur'
						},
						{
							validator: validateConfirmPassword,
							trigger: 'blur'
						}
					],
					//
					// captcha: [{
					// 	// required: true,
					// 	message: '验证码不能为空',
					// 	trigger: 'blur'
					// }],

					sms: [{
						required: true,
						message: '短信验证码不能为空',
						trigger: 'blur'
					}]
				},
				captchaPath: ''
			}
		},
		created() {
			this.getCaptcha()
			this.dataForm.flag = true
			this.dataForm.switcher = true
			this.dataForm.buttonContent = '发送验证码'
		},
		methods: {
			// 提交表单
			dataFormSubmit() {
				this.$refs['dataForm'].validate((valid) => {
					if (valid) {
						this.$http({
							method: 'post',
							url: this.$http.adornEkUrl('/login/forgetPassword'),
							params: this.$http.adornParams({
								'phone': this.dataForm.userName,
								'newPassword': this.dataForm.password,
								'smsCode': this.dataForm.sms
							})
						}).then(({
							data
						}) => {
							if (data && data.code === 2000) {
								this.$message.success(data.msg)
								this.$router.replace({
									name: 'login'
								})
							} else {
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
						//清除人人的token
						this.$cookie.delete('token')

						this.getCaptcha()
						this.$message.error(data.msg)
					}
				})
			},

			// 获取验证码
			getCaptcha: function() {
				this.dataForm.uuid = getUUID()
				this.captchaPath = this.$http.adornEkUrl(`/captcha/createCaptcha?uuid=${this.dataForm.uuid}`)
			},

			// 获取短信验证码
			getSms: function() {
				this.getCaptcha()
				if (this.dataForm.switcher != true) {
					this.$message.error("30秒内只能发送一次")
				} else {

					if (!isMobile(this.dataForm.userName)) {
						this.$message.error("手机号码错误")
					} else {

						this.$http({
							url: this.$http.adornEkUrl('/sms/smsSend'),
							method: 'post',
							params: this.$http.adornParams({
								'phone': this.dataForm.userName,
							})
						}).then(({
							data
						}) => {
							this.dataForm.switcher = false
							this.dataForm.flag = true
							this.nextTick()
							if (data && data.code === 2000) {
								this.$message.success(data.msg)
							} else {
								this.$message.error(data.msg)
							}
						})
					}
				}
			},

			nextTick: function() {
				this.dataForm.second = 30
				this.dataForm.timer = setInterval(this.handleCount, 1000)
			},

			handleCount: function() {
				this.dataForm.second -= 1
				if (this.dataForm.second <= 0) {
					clearInterval(this.dataForm.timer)
					this.dataForm.flag = false
					this.dataForm.switcher = true
					this.dataForm.buttonContent = '发送验证码'
				} else {
          this.dataForm.buttonContent = this.dataForm.second + '秒后可以重发'
				}
			},

			checkCaptcha() {
				if (this.dataForm.captcha.length === 5 && this.dataForm.switcher) {
					this.$http({
						url: this.$http.adornEkUrl('/captcha/checkoutCaptcha'),
						method: 'post',
						params: this.$http.adornParams({
							'uuid': this.dataForm.uuid,
							'code': this.dataForm.captcha
						})
					}).then(({
						data
					}) => {
						if (data && data.code === 2000) {
							this.dataForm.flag = false
						} else {
							this.$message.error('验证码不正确')
						}
					})
				}
			},

		},
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

		.get-sms {
			width: 100%;
		}

	}
</style>
