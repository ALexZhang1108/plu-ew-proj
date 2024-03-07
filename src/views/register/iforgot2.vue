<template>
  <div class="login-container">
    <div class="content-container">
      <a href="#" class="back-button">
        <svg-icon icon-class="back" />
      </a>
      <el-form ref="loginForm" :model="registerForm" :rules="registerRules" class="register-form" autocomplete="on" label-position="left">

        <div class="title-container">
          <h3 class="title-text">FORGOT PASSWORD</h3>
        </div>
        <div class="email-icon">
          <svg-icon icon-class="email2" />
        </div>
        <div class="iforgot-container1">
          <h4 class="iforgot-title">An e-mail has been sent to the following e-mail address:</h4>
        </div>

        <div class="iforgot-container2">
          <h5 class="iforgot-content">xxxxx@xxxxxx.com</h5>
        </div>
        <div class="iforgot-container2">
          <h5 class="iforgot-content">Please check your email, we have sent you a link to reset your password. </h5>
        </div>

        <el-button :type="primary" class="content-button" @click.native.prevent="handleLogin">Sign in</el-button>
      </el-form>

    </div>
  </div>
</template>

<script>
import { validUsername } from '@/utils/validate'

export default {
  name: 'Login',

  data() {
    const validateUsername = (rule, value, callback) => {
      if (!validUsername(value)) {
        callback(new Error('Please enter the correct user name'))
      } else {
        callback()
      }
    }
    const validatePassword = (rule, value, callback) => {
      if (value.length < 6) {
        callback(new Error('The password can not be less than 6 digits'))
      } else {
        callback()
      }
    }
    return {
      registerForm: {
        username: 'admin',
        password: '111111'
      },
      registerRules: {
        username: [{ required: true, trigger: 'blur', validator: validateUsername }],
        password: [{ required: true, trigger: 'blur', validator: validatePassword }]
      },
      passwordType: 'password',
      capsTooltip: false,
      loading: false,
      showDialog: false,
      redirect: undefined,
      agree: false,
      otherQuery: {}
    }
  },
  watch: {
    $route: {
      handler: function(route) {
        const query = route.query
        if (query) {
          this.redirect = query.redirect
          this.otherQuery = this.getOtherQuery(query)
        }
      },
      immediate: true
    }
  },
  created() {
    // window.addEventListener('storage', this.afterQRScan)
  },
  mounted() {
    if (this.registerForm.username === '') {
      this.$refs.username.focus()
    } else if (this.registerForm.password === '') {
      this.$refs.password.focus()
    }
  },
  destroyed() {
    // window.removeEventListener('storage', this.afterQRScan)
  },
  methods: {
    checkCapslock(e) {
      const { key } = e
      this.capsTooltip = key && key.length === 1 && (key >= 'A' && key <= 'Z')
    },
    showPwd() {
      if (this.passwordType === 'password') {
        this.passwordType = ''
      } else {
        this.passwordType = 'password'
      }
      this.$nextTick(() => {
        this.$refs.password.focus()
      })
    },
    handleLogin() {
      this.$refs.loginForm.validate(valid => {
        if (valid) {
          this.loading = true
          this.$store.dispatch('user/login', this.registerForm)
            .then(() => {
              this.$router.push({ path: this.redirect || '/', query: this.otherQuery })
              this.loading = false
            })
            .catch(() => {
              this.loading = false
            })
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
    getOtherQuery(query) {
      return Object.keys(query).reduce((acc, cur) => {
        if (cur !== 'redirect') {
          acc[cur] = query[cur]
        }
        return acc
      }, {})
    }
    // afterQRScan() {
    //   if (e.key === 'x-admin-oauth-code') {
    //     const code = getQueryObject(e.newValue)
    //     const codeMap = {
    //       wechat: 'code',
    //       tencent: 'code'
    //     }
    //     const type = codeMap[this.auth_type]
    //     const codeName = code[type]
    //     if (codeName) {
    //       this.$store.dispatch('LoginByThirdparty', codeName).then(() => {
    //         this.$router.push({ path: this.redirect || '/' })
    //       })
    //     } else {
    //       alert('第三方登录失败')
    //     }
    //   }
    // }
  }
}
</script>

<style lang="scss">
/* 修复input 背景不协调 和光标变色 */
/* Detail see https://github.com/PanJiaChen/vue-element-admin/pull/927 */

$bg:#283443;
$light_gray:#fff;
$cursor: #fff;

@supports (-webkit-mask: none) and (not (cater-color: $cursor)) {
  .login-container .el-input input {
    color: $cursor;
  }
}

/* reset element-ui css */
.login-container {
  .el-input {
    display: inline-block;
    height: 47px;
    width: 85%;
    border-radius: 67px;

    input {
      background: transparent;
      border: 0px;
      -webkit-appearance: none;
      border-radius: 67px;
      padding: 12px 5px 12px 15px;
      color: #000000;
      height: 47px;
      caret-color: #000000;
      font-size: 15px;
      font-family: Inter, sans-serif;

      &:-webkit-autofill {
        box-shadow: 0 0 0px 1000px $bg inset !important;
        -webkit-text-fill-color: $cursor !important;
      }
    }
  }

  .el-form-item {
    border: 1px solid rgba(255, 255, 255, 0.1);
    background: rgba(0, 0, 0, 0.1);
    border-radius: 67px;
    color: #454545;
  }

  .el-button {
    border-radius: 99px;
  }
}
</style>

<style lang="scss" scoped>
$bg:#252856;
$dark_gray:#889aa4;
$light_gray:#eee;

.el-input {
  border-radius: 40px; /* Adjust this value to change the border radius */
}
.forgot-password {
  display: block;
  text-align: center;
  margin-top: 2px ;
  margin-bottom: 2px;
  padding: 10px;
  color: #ea5820;
}

.back-button {
  position: absolute; // 使用绝对定位
  top: 36px; // 从顶部偏移10px
  left: 30px; // 从左边偏移10px
  width: 44px; // 设置宽度
  height: 44px; // 设置高度

  img {
    width: 100%; // 让图片填满整个按钮
    height: 100%; // 让图片填满整个按钮
  }
  .svg-icon {
    width: 44px; // 设置图标宽度
    height: 44px; // 设置图标高度
  }
}

.login-container {
  min-height: 100%;
  width: 100%;
  background-color: $bg;
  overflow: hidden;

  .title-container {
    position: absolute; // 设置绝对定位
    top: 0px; // 从顶部向上偏移150px
    left: 0px; //别动
    width: 100%; // 设置宽度为100%，使其占满整个容器
    text-align: center; // 设置文本居中

  }

  .title-text {
    font-family: Inter, sans-serif;
    font-size: 30px;
    color: $bg;
    font-weight: bold;
    margin-top: 20px;
  }

  .minititle-container {
    margin: 10px; // 根据需要调整间距
    text-align: left; // 将文本靠左对齐

  }

  .minitext {
    font-size: 14px; // 设置字体大小
    font-family: "Microsoft JhengHei UI"; // 设置字体
    color: $bg; // 设置字体颜色
  }

  .minitext2{
    font-size: 14px; // 设置字体大小
    font-family: Inter, sans-serif; // 设置字体
    font-weight: normal;
    color: #3664ae; // 设置字体颜色
    margin-top: 10px;
    margin-bottom: 8px;
  }

  .content-container {
    background-color: #f2f2f2; // 设置背景颜色
    border-radius: 30px; // 设置圆角
    padding: 20px; // 设置内边距
    position: absolute; // 设置绝对定位
    top: 50%; // 从顶部偏移50%
    left: 50%; // 从左边偏移50%
    transform: translate(-50%, -50%); // 使用 transform 属性将元素向左和向上移动50%
    height: 521px;// prototype 里就随便依托
    width: 534px; // prototype 里就随便依托
    text-align: center; // 设置文本居中

  }

  .email-icon{
    margin: 10px; // 根据需要调整间距
    text-align: center;

    .svg-icon {
      width: 85px; // 设置图标宽度
      height: 85px; // 设置图标高度
    }
  }
  .iforgot-container1 {
    margin: 10px; // 根据需要调整间距
    margin-top: 34px;
    text-align: center;

    .iforgot-title {
      font-size: 20px; // 设置字体大小
      font-family: "Microsoft JhengHei UI"; // 设置字体
      color: $bg; // 设置字体颜色
    }
  }

  .iforgot-container2{
    margin: 10px; // 根据需要调整间距
    text-align: center; // 将文本靠左对齐
    margin-bottom: 30px;
    .iforgot-content {
      font-size: 14px; // 设置字体大小
      font-family: "Microsoft JhengHei UI"; // 设置字体
      color: $bg; // 设置字体颜色
    }
  }

  .checkbox-button-container {
    display: flex; // 使用 flex 布局

    align-items: center; // 使三个元素在垂直方向上居中

    .terms-container {
      display: flex; // 使用 flex 布局
      align-items: center; // 使元素在垂直方向上居中
      justify-content: flex-start; // 使元素在水平方向上靠左对齐
      padding: 0; // 移除内边距
      margin: 0; // 移除外边距
    }

    .terms-checkbox {
      flex:1;
      margin: 0; // 移除外边距
      color: #6c6aeb;
    }

    .minitext3{
      flex:5;
      font-size: 12px; // 设置字体大小
      font-family: Inter; // 设置字体
      font-weight: lighter;
      color: $bg; // 设置字体颜色
      //靠左对齐
      text-align: left;

    }
  }

  .register-form {
    position: relative;
    width: 520px;
    max-width: 100%;
    padding: 70px 30px 18px 30px;
    margin: 0 auto;
    overflow: hidden;
  }

  .tips {
    font-size: 14px;
    color: #fff;
    margin-bottom: 10px;

    span {
      &:first-of-type {
        margin-right: 16px;
      }
    }
  }

  .svg-container {
    padding: 6px 5px 6px 15px;
    color: $bg;
    vertical-align: middle;
    width: 30px;
    display: inline-block;
  }

  .show-pwd {
    position: absolute;
    right: 10px;
    top: 7px;
    font-size: 16px;
    color: $dark_gray;
    cursor: pointer;
    user-select: none;
  }

  .thirdparty-button {
    position: absolute;
    right: 0;
    bottom: 6px;
  }

  .content-button {

    width: auto; // 设置按钮宽度
    height: 53px; // 设置按钮高度

    border-radius: 81px;
    line-height: 0px; // 让按钮内的文字垂直居中
    background-color: #6c6aeb; // 设置按钮背景颜色
    text-align: center; // 设置按钮内的文字居中
    color: $light_gray; // 设置按钮内的文字颜色

    font-size: 20px; // 设置按钮内的文字大小
  }

  .social-icons {
    display: flex;
    justify-content: space-between;
    width: 256px;
    margin: 0 auto; // 让社交图标在容器中居中
    margin-top: 0px;
    margin-bottom: 10px; // 根据需要调整

    .social-icon {
      display: flex;
      width: 40px; // 根据需要调整
      height: 40px; // 根据需要调整
      border-radius: 50%;
      background-color: #d9d9d9; // 设置社交图标背景颜色
      justify-content: center;
      align-items: center;
      margin: 0 auto; // 让社交图标在容器中居中

      .svg-icon {
        width: 26px; // 根据需要调整
        height: 26px; // 根据需要调整
      }
    }
  }

  @media (max-width: 600px) {
    .content-container {
      width: 90%; // 调整宽度
      height: auto; // 调整高度
      overflow: auto; // 添加滚动条
    }
  }
}
</style>
