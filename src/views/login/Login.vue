<template>
  <el-row class="container">
    <el-col :span="16">
      <div class="left-container">
        <div class="title"><span>登录</span><a id="warning"></a></div>
        <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px"
                 class="input-container">
          <el-form-item prop="username" label-width="0">
            <el-input v-model="ruleForm.username" placeholder="用户名或邮箱"></el-input>
          </el-form-item>
          <el-form-item prop="password" label-width="0">
            <el-input type="password" v-model="ruleForm.password" placeholder="密码"></el-input>
          </el-form-item>
          <el-form-item prop="captcha" label-width="0">
            <el-col :span="14">
              <el-input v-model="ruleForm.captcha" placeholder="验证码"></el-input>
            </el-col>
              <img :src="changeImage" @click="handleClickImage" class="codeImage" />
          </el-form-item>
        </el-form>
        <div class="action-container">
          <el-col :span="18">
            <span @click="submitForm()">提交</span>
          </el-col>
          <el-col :span="6">
            <router-link to="forget"><a>忘记密码</a></router-link>
          </el-col>
        </div>
      </div>
    </el-col>
    <el-col :span="8">
      <div class="right-container">
        <div class="register-container">
          <span><router-link to="register"><a>注册</a></router-link></span>
        </div>
      </div>
    </el-col>
  </el-row>
</template>

<script>
  import {GetCaptcha} from "@/utils/request";

  export default {
    name: 'Login',
    data() {
      return {
        redirect: undefined,
        changeImage: '',
        captchaId: '',
        ruleForm: {
          username: '',
          password: '',
          captcha: ''
        },
        rules: {
          username: [{
            required: true,
            message: '用户名或邮箱',
            trigger: 'blur'
          }],
          password: [{
            required: true,
            message: '请输入密码',
            trigger: 'blur'
          },
            {
              min: 6,
              max: 18,
              message: '长度在 6 到 18 个字符',
              trigger: 'blur'
            }
          ],
          captcha: [{
            required: true,
            message: '请填写验证码',
            trigger: 'blur'
          }]
        }
      };
    },
    watch: {
      $route: {
        handler: function (route) {
          this.redirect = route.query && route.query.redirect
        },
        immediate: true
      }
    },
    methods: {
      submitForm() {
        this.$refs.ruleForm.validate((valid) => {
          if (valid) {
            this.$store.dispatch('user/login', this.ruleForm).then(() => {
              this.$router.push({path: this.redirect || '/'})
            })
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
      handleClickImage(){
        GetCaptcha().then(res =>{
          this.changeImage = res.data.content
          this.captchaId = res.data.captchaId
        })
      }
    },
    mounted() {
       this.handleClickImage()  //加载刷新验证码
    }
  }
</script>

<style lang="scss">
  /* 修复input 背景不协调 和光标变色 */
  /* Detail see https://github.com/PanJiaChen/vue-element-admin/pull/927 */
  @import "~@/styles/login.scss";
  .codeImage {
    width: 110px;
    height: 34px;
    margin-top: 40px;
    // border: 1px solid #007ACC;
  }
</style>
