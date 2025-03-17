<template>
  <div class="container">
    <div class="login-card">
      <div class="login-content">
        <div class="login-title">流浪动物救助平台</div>
        <el-form :model="user" :rules="rules" ref="userForm">
          <el-form-item prop="username">
            <el-input size="medium" prefix-icon="el-icon-user" v-model="user.username" placeholder="请输入账号"></el-input>
          </el-form-item>
          <el-form-item prop="password">
            <el-input size="medium" prefix-icon="el-icon-lock" show-password v-model="user.password" placeholder="请输入密码"></el-input>
          </el-form-item>
          <div class="btn-group">
            <el-button type="primary" @click="login">登录</el-button>
            <el-button type="warning" @click="$router.push('/register')">注册账号</el-button>
            <el-button type="success" @click="handlePass">找回密码</el-button>
          </div>
        </el-form>
      </div>

      <el-dialog title="找回密码" :visible.sync="dialogFormVisible" width="30%">
        <el-form label-width="100px" size="small">
          <el-form-item label="用户名">
            <el-input v-model="pass.username" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="手机号">
            <el-input v-model="pass.phone" autocomplete="off"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormVisible = false">取 消</el-button>
          <el-button type="primary" @click="passwordBack">重置密码</el-button>
        </div>
      </el-dialog>
    </div>
  </div>
</template>

<script>
import {resetRouter, setRoutes} from "@/router";

export default {
  name: "Login",
  data() {
    return {
      user: {},
      pass: {},
      dialogFormVisible: false,
      rules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 3, max: 10, message: '长度在 3 到 5 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 1, max: 20, message: '长度在 1 到 20 个字符', trigger: 'blur' }
        ],
      }
    }
  },
  created() {
    // 重置路由
    resetRouter()
  },
  methods: {
    login() {
      this.$refs['userForm'].validate((valid) => {
        if (valid) {  // 表单校验合法
          this.request.post("/user/login", this.user).then(res => {
            if(res.code === '200') {
              localStorage.setItem("user", JSON.stringify(res.data))  // 存储用户信息到浏览器
              localStorage.setItem("menus", JSON.stringify(res.data.menus))  // 存储用户信息到浏览器

              // 动态设置当前用户的路由
              setRoutes()
              this.$router.push("/front/home")
              this.$message.success("登录成功")
            } else {
              this.$message.error(res.msg)
            }
          })
        }
      });
    },
    handlePass() {
      this.dialogFormVisible = true
      this.pass = {}
    },
    passwordBack() {
      this.request.put("/user/reset", this.pass).then(res => {
        if (res.code === '200') {
          this.$message.success("重置密码成功，新密码为：123，请尽快修改密码")
          this.dialogFormVisible = false
        } else {
          this.$message.error(res.msg)
        }
      })
    }
  }
}
</script>

<style>
.container {
  height: 100vh;
  overflow: hidden;
  background: linear-gradient(135deg, #3498db, #2c3e50);
  display: flex;
  align-items: center;
  justify-content: center;
  color: #666;
}

.login-card {
  width: 380px;
  background-color: rgba(255, 255, 255, 0.95);
  border-radius: 8px;
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.15);
  overflow: hidden;
}

.login-content {
  padding: 40px;
}

.login-title {
  text-align: center;
  font-size: 28px;
  font-weight: 600;
  margin-bottom: 40px;
  color: #2c3e50;
  letter-spacing: 2px;
  position: relative;
}

.login-title:after {
  content: "";
  position: absolute;
  bottom: -10px;
  left: 50%;
  transform: translateX(-50%);
  width: 60px;
  height: 3px;
  background-color: #3498db;
  border-radius: 3px;
}

.el-form-item {
  margin-bottom: 25px;
}

.el-input__inner {
  height: 45px;
  border-radius: 4px;
}

.btn-group {
  display: flex;
  justify-content: space-between;
  margin-top: 30px;
}

.btn-group .el-button {
  flex: 1;
  margin: 0 5px;
  padding: 12px 0;
  font-size: 14px;
  border-radius: 4px;
}

.btn-group .el-button:first-child {
  margin-left: 0;
}

.btn-group .el-button:last-child {
  margin-right: 0;
}

.el-button--primary {
  background-color: #3498db;
  border-color: #3498db;
}

.el-button--warning {
  background-color: #f39c12;
  border-color: #f39c12;
}

.el-button--success {
  background-color: #27ae60;
  border-color: #27ae60;
}
</style>
