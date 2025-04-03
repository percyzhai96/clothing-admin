<template>
  <div class="login">
    <vue-particles
      class="login-bg"
      color="#39AFFD"
      :particleOpacity="0.7"
      :particlesNumber="100"
      shapeType="circle"
      :particleSize="4"
      linesColor="#8DD1FE"
      :linesWidth="1"
      :lineLinked="true"
      :lineOpacity="0.4"
      :linesDistance="150"
      :moveSpeed="3"
      :hoverEffect="true"
      hoverMode="grab"
      :clickEffect="true"
      clickMode="push"
    />
    <!-- 登录面板 -->
    <div class="login-box" @keyup.enter="submitForm">
      <div class="login-box-title">🌸RUI-ADMIN</div>
      <div class="login-box-from">
        <el-form
          :model="loginForm"
          :rules="rules"
          ref="loginForm"
          class="demo-ruleForm"
        >
          <el-form-item prop="username">
            <el-input
              v-model="loginForm.username"
              placeholder="请输入用户名"
              size="medium"
            >
              <el-button slot="prepend" icon="el-icon-user" />
            </el-input>
          </el-form-item>
          <el-form-item prop="password">
            <el-input
              :type="!pwdShow ? 'password' : 'text'"
              v-model="loginForm.password"
              placeholder="请输入密码"
              size="medium"
            >
              <i
                slot="prepend"
                @click="pwdShow = !pwdShow"
                class="el-icon-key"
              />
            </el-input>
          </el-form-item>
          <el-form-item prop="code">
            <el-input
              type="text"
              clearable
              v-model.trim="loginForm.code"
              placeholder="验证码"
            >
              <template slot="append">
                <div
                  class="login-code"
                  v-html="loginForm.codeSvg"
                  @click="getCodeSvg"
                ></div>
              </template>
            </el-input>
          </el-form-item>
          <el-form-item>
            <el-button
              type="primary"
              size="medium"
              :loading="loading"
              style="width: 100%"
              @click.native="submitForm"
              >log on</el-button
            >
          </el-form-item>
        </el-form>
      </div>
    </div>
  </div>
</template>

<script>
import { showLoading } from '@/utils'
import { mapActions } from 'vuex'
export default {
  data() {
    return {
      loading: false, //登陆状态
      pwdShow: false,
      loginForm: {
        // 登陆表单
        username: '',
        password: '',
        code: '',
        codeSvg: '',
        key: '',
      },
      rules: {
        //登陆验证规则
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          {
            min: 2,
            max: 18,
            message: '长度在 2 到 18 个字符',
            trigger: 'blur',
          },
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          {
            min: 6,
            max: 16,
            message: '长度在 6 到 16 个字符',
            trigger: 'blur',
          },
        ],
      },
    }
  },
  mounted() {
    this.getCodeSvg()
  },
  methods: {
    ...mapActions('user', ['login', 'getLoginCaptcha']),
    submitForm() {
      this.$refs.loginForm.validate((valid) => {
        if (!valid) {
          return false
        }
        this.loading = true
        this._login()
      })
    },
    getCodeSvg() {
      showLoading('正在获取验证码')
      this.getLoginCaptcha().then((res) => {
        this.loginForm.codeSvg = res.codeSvg || ''
        this.loginForm.key = res.key || ''
        this.loading = false
      })
    },
    _login() {
      this.login(this.loginForm)
        .then((res) => {
          this.loading = true
          this.$router
            .push({
              path: this.$route.query.redirect || '/index',
            })
            .catch((err) => {})
          if (!res.data.userInfo.login_time) return false
          this.$notify({
            title: '欢迎回来',
            message: '上次登陆时间：' + res.data.userInfo.login_time,
            offset: 100,
          })
        })
        .catch((error) => {
          this.loading = false
        })
    },
  },
}
</script>

<style lang="scss">
@import '@/assets/css/login.scss';
</style>
