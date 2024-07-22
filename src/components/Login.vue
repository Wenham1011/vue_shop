<template>
  <div class="login_container">
    <div class="login_box">
      <!--    logo区域-->
      <div class="avatar_box">
        <img src="../assets/logo.png">
      </div>
      <!--      登录表单区域-->
      <el-form class="login_form" :model="LoginForm" :rules="loginFormRules" ref="loginFormRef">
        <!--        用户名-->
        <el-form-item prop="username">
          <el-input v-model="LoginForm.username">
            <i class="fa fa-user-circle" aria-hidden="true"></i>
          </el-input>
        </el-form-item>
        <!--        密码-->
        <el-form-item prop="password">
          <el-input type="password" v-model="LoginForm.password">
            <i class="fa fa-lock" aria-hidden="true"></i>
          </el-input>
        </el-form-item>
        <!--      按钮-->
        <el-form-item class="btns">
          <el-button type="primary" round @click="login">Login</el-button>
          <el-button type="info" round @click="resetLoginForm">reset</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import 'font-awesome/css/font-awesome.min.css'

export default {
  data() {
    return {
      LoginForm: {
        username: 'admin',
        password: '123456'
      },
      loginFormRules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 4, max: 15, message: '长度在 4 到 15 个字符', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    resetLoginForm() {
      // console.log(this)
      this.$refs.loginFormRef.resetFields()
    },
    login() {
      this.$refs.loginFormRef.validate(async valid => {
        if (!valid) return
        const { data: res } = await this.$http.post('login', this.LoginForm)
        if (res.meta.status !== 200) return this.$message.error('登录失败')
        this.$message.success('登录成功')
        console.log(res)
        // 存储token
        window.sessionStorage.setItem('token', res.data.token)
        // 跳转到主页
        this.$router.push('/home')
      })
    }
  }
}
</script>

<style lang="less" scoped>
.login_container {
  background: #2b4b6b;
  height: 100%;
  display: flex;
  flex-direction: row; //主轴
  justify-content: center; //主轴对其方式
  align-items: center; //交叉轴对齐方式
}

.login_box {
  width: 450px;
  height: 300px;
  background: #fff;
  border-radius: 3px;
  position: relative;
}

.avatar_box {
  height: 100px;
  width: 100px;
  border: 1px solid #eee;
  border-radius: 50%;
  position: absolute;
  left: 50%; //相对外一层的容器来说，自身移动外层容器的50%
  transform: translate(-50%, -50%); //向左移动自身大小的50%,再向上
  img {
    width: 100%;
    height: 100%;
    border-radius: 50%;
    background: #eee;
    box-shadow: 0 0 5px #2b4b6b;
  }
}

.login_form {
  position: absolute;
  bottom: 0;
  width: 100%;
  padding: 20px;
  box-sizing: border-box;
}

</style>
