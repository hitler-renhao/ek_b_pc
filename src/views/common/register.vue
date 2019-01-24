<template>
	<div class="site-wrapper site-page--login">
		<div class="site-content__wrapper">
			<div class="site-content">
				<div class="brand-info">
					<h2 class="brand-info__text">ek商户管理平台</h2>
					<p class="brand-info__intro">—— 品牌成就梦想！</p>
				</div>
				<div class="login-main">
					<h3 class="login-title">注册</h3>
					<el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="doEkRegister()" status-icon>
						<el-form-item prop="userName">
							<el-input v-model="dataForm.userName" placeholder="帐号"></el-input>
						</el-form-item>
						<el-form-item prop="password">
							<el-input v-model="dataForm.password" type="password" placeholder="请输入密码"></el-input>
						</el-form-item>
						<el-form-item prop="password2">
							<el-input v-model="dataForm.password2" type="password" placeholder="请再次输出入密码"></el-input>
						</el-form-item>

						<el-form-item>
							<el-button class="login-btn-submit" type="primary" @click="doEkRegister()">注册</el-button>
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
					password2: ''
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
					]
				},
				captchaPath: ''
			}
		},

		methods: {
			//注册账号
			doEkRegister() {
				this.$refs['dataForm'].validate((valid) => {
					if (valid) {

						this.$http({
							method: 'post',
							url: this.$http.adornEkUrl('/login/publicRegister'),
							params: this.$http.adornParams({
								'iphone': this.dataForm.userName,
								'password': this.dataForm.password
							})
						}).then(({
							data
						}) => {
							if (data && data.code === 200) {
								this.$message.success("注册成功")
								this.$router.replace({
									name: 'login'
								})
							} else {
								this.$message.error(data.msg)
							}
						})

					}
				})
			}
		}
	}
</script>

<style lang="scss">
	.site-wrapper.site-page--login {
		position: absolute;
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
			padding: 30px 400px 30px 30px;
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
			font-size: 20px;
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
	}
</style>
