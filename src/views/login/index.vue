<template>
  <div class="auth-page-container">
  <el-row type="flex">
    <el-col :span="12" :offset="2">  
    <div class="login-container">
        <el-form
          ref="loginForm"
          :model="loginForm"
          :rules="loginRules"
          class="login-form"
          autocomplete="on"
          label-position="left"
        >
          <div class="title-container">
            <h3 class="title">标注云平台</h3>
          </div>

          <el-form-item prop="username">
            <span class="svg-container">
              <svg-icon icon-class="user" />
            </span>
            <el-input
              ref="username"
              v-model="loginForm.username"
              placeholder="User email 账户邮箱"
              name="username"
              type="text"
              tabindex="1"
              autocomplete="on"
            />
          </el-form-item>

          <el-tooltip
            v-model="capsTooltip"
            content="Caps lock is On"
            placement="right"
            manual
          >
            <el-form-item prop="password">
              <span class="svg-container">
                <svg-icon icon-class="password" />
              </span>
              <el-input
                :key="passwordType"
                ref="password"
                v-model="loginForm.password"
                :type="passwordType"
                placeholder="Password 密码"
                name="password"
                tabindex="2"
                autocomplete="on"
                @keyup.native="checkCapslock"
                @blur="capsTooltip = false"
                @keyup.enter.native="handleLogin"
              />
              <span class="show-pwd" @click="showPwd">
                <svg-icon
                  :icon-class="passwordType === 'password' ? 'eye' : 'eye-open'"
                />
              </span>
            </el-form-item>
          </el-tooltip>
          <el-link type="info">Forgot your password? 忘记密码</el-link>
          <el-button
            :loading="loading"
            class="normal-button"
            type="primary"
            style="width: 100%; margin-bottom: 30px"
            @click.native.prevent="handleLogin"
            >Login 登录</el-button
          >

          <div style="position: relative">
            <div class="tips">
              <span>测试账号1 : admin</span><br />
              <span>测试密码1 : any</span>
            </div>
          </div>
        </el-form>

      </div>
     </el-col>
     <el-col :span="6" :offset="2">
       <div class = "info-container">
         <div class = "info-box">
           <h5 class="info">
          • 高效为您寻找外包团队, 兼职标注师<br>
          • 为公司外部人员提供快速收入渠道<br>
          • 保障数据安全<br>
          • 保证标注质量<br>
          </h5>
          </div>
       </div></el-col>
    </el-row>
  </div>
</template>

<script>
import { validUsername } from "@/utils/validate";

export default {
  name: "Login",
  components: { },
  data() {
    const validateUsername = (rule, value, callback) => {
      if (!validUsername(value)) {
        callback(new Error("Please enter the correct user name"));
      } else {
        callback();
      }
    };
    const validatePassword = (rule, value, callback) => {
      if (value.length < 6) {
        callback(new Error("The password can not be less than 6 digits"));
      } else {
        callback();
      }
    };
    return {
      loginForm: {
        username: "admin",
        password: "111111",
      },
      loginRules: {
        username: [
          { required: true, trigger: "blur", validator: validateUsername },
        ],
        password: [
          { required: true, trigger: "blur", validator: validatePassword },
        ],
      },
      passwordType: "password",
      capsTooltip: false,
      loading: false,
      showDialog: false,
      redirect: undefined,
      otherQuery: {},
    };
  },
  watch: {
    $route: {
      handler: function (route) {
        const query = route.query;
        if (query) {
          this.redirect = query.redirect;
          this.otherQuery = this.getOtherQuery(query);
        }
      },
      immediate: true,
    },
  },
  created() {
    // window.addEventListener('storage', this.afterQRScan)
  },
  mounted() {
    if (this.loginForm.username === "") {
      this.$refs.username.focus();
    } else if (this.loginForm.password === "") {
      this.$refs.password.focus();
    }
  },
  destroyed() {
    // window.removeEventListener('storage', this.afterQRScan)
  },
  methods: {
    checkCapslock(e) {
      const { key } = e;
      this.capsTooltip = key && key.length === 1 && key >= "A" && key <= "Z";
    },
    showPwd() {
      if (this.passwordType === "password") {
        this.passwordType = "";
      } else {
        this.passwordType = "password";
      }
      this.$nextTick(() => {
        this.$refs.password.focus();
      });
    },
    handleLogin() {
      this.$refs.loginForm.validate((valid) => {
        if (valid) {
          this.loading = true;
          this.$store
            .dispatch("user/login", this.loginForm)
            .then(() => {
              this.$router.push({
                path: this.redirect || "/",
                query: this.otherQuery,
              });
              this.loading = false;
            })
            .catch(() => {
              this.loading = false;
            });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    getOtherQuery(query) {
      return Object.keys(query).reduce((acc, cur) => {
        if (cur !== "redirect") {
          acc[cur] = query[cur];
        }
        return acc;
      }, {});
    },
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
  },
};
</script>

<style lang="scss">
/* 修复input 背景不协调 和光标变色 */
/* Detail see https://github.com/PanJiaChen/vue-element-admin/pull/927 */

$bg: #667286;
$major_color1: #fbe6bc;
$cursor: #d1b8c5;

@supports (-webkit-mask: none) and (not (cater-color: $cursor)) {
  .login-container .el-input input {
    color: $cursor;
  }
}

/* reset element-ui css */
.auth-page-container {
  .login-container {
    .el-input {
      display: inline-block;
      height: 47px;
      width: 85%;

      input {
        background: transparent;
        border: 0px;
        -webkit-appearance: none;
        border-radius: 0px;
        padding: 12px 5px 12px 15px;
        color: $major_color1;
        height: 47px;
        caret-color: $cursor;

        &:-webkit-autofill {
          box-shadow: 0 0 0px 1000px $bg inset !important;
          -webkit-text-fill-color: $cursor !important;
        }
      }
    }

    .el-form-item {
      border: 1px solid rgba(255, 255, 255, 0.1);
      background: rgba(0, 0, 0, 0.1);
      border-radius: 5px;
      color: #c4959f;
    }
  }
}
</style>

<style lang="scss" scoped>
$bg: #667286;
$major_color1: #fbe6bc;
$major_color2: #d1b8c5;
$major_color3: #b6cbd6;
.auth-page-container {
  background-color: $major_color3;
  min-height: inherit;
  width: 100%;
  overflow: hidden;

  .el-row {
    margin-top: 10%;
    margin-bottom: 20px;
    min-height: 800px;
    &:last-child {
      margin-bottom: 0;
    }
  }
  .el-col {
    max-height: 500px;
  }
  .el-link{
    color:$major_color3;
    margin-bottom: 20px;
    font-style: italic;
    text-align: center;
  }
  .info-container {
    background-color: rgba(209,184,197, 0.6);
    border-radius: 100px;
    height: 100%;
    padding: 20px 20px 20px 20px;

    .info-box{
      position: relative;
      .info{
        text-align: middle;
        padding: 50px 35px 0;
        margin: 0 auto;
        overflow: hidden;
        font-family: 微软雅黑;
        color: $major_color1;
        font-size: 20px;
        margin-left: 20px;
        }
      }
  }
  .login-container {

    background-color: rgba(102, 114, 134, 0.6);
    border-radius: 100px;
    padding: 20px 20px 20px 20px;
    height: 100%;
    
    .login-form {
      position: relative;
      width: 520px;
      max-width: 100%;
      padding: 50px 35px 0;
      margin: 0 auto;
      overflow: hidden;
    }

    .tips {
      font-family: 微软雅黑;
      font-size: 16px;
      color: $major_color1;
      margin-bottom: 10px;

      span {
        &:first-of-type {
          margin-right: 16px;
        }
      }
    }

    .svg-container {
      padding: 6px 5px 6px 15px;
      color: $major_color2;
      vertical-align: middle;
      width: 30px;
      display: inline-block;
    }

    .title-container {
      position: relative;

      .title {
        font-family: 微软雅黑;
        font-size: 60px;
        color: $major_color1;
        text-shadow: 5px, 5px, 5px, #6b6b6b;
        margin: 0px auto 40px auto;
        text-align: center;
        font-weight: bold;
        font-style: italic;
      }
    }
    .show-pwd {
      position: absolute;
      right: 10px;
      top: 7px;
      font-size: 16px;
      color: $major_color2;
      cursor: pointer;
      user-select: none;
    }

    .normal-button {
      background-color: $major_color2;
      color: $bg;
      border: none;
      border-radius: 3px;
      font-family: 微软雅黑;
    }
    .thirdparty-button {
      background-color: $major_color2;
      color: $bg;
      font-family: 微软雅黑;
      border: none;
      border-radius: 3px;
      position: absolute;
      right: 0;
      bottom: 6px;
    }

    @media only screen and (max-width: 470px) {
      .thirdparty-button {
        display: none;
      }
    }
  }
  
}
</style>
