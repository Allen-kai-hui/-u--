<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/home/user' }">管理员列表</el-breadcrumb-item>
      <el-breadcrumb-item>{{ title }}</el-breadcrumb-item>
    </el-breadcrumb>

    <el-form
      :model="ruleForm"
      :rules="rules"
      ref="ruleForm"
      label-width="100px"
      class="demo-ruleForm"
    >
      <el-form-item label="所属角色" prop="roleid">
        <el-select v-model="ruleForm.roleid" placeholder="请选择">
          <el-option
            :label="item.rolename"
            :value="item.id"
            v-for="(item) in userlist"
            :key="item.id"
          ></el-option>
        </el-select>
      </el-form-item>

      <el-form-item label="用户名" prop="username">
        <el-input v-model="ruleForm.username"></el-input>
      </el-form-item>

      <el-form-item label="密码" prop="password">
        <el-input v-model="ruleForm.password"></el-input>
      </el-form-item>

      <el-form-item label="状态" prop="status">
        <el-switch v-model="ruleForm.status"></el-switch>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="submitForm('ruleForm')">{{buttitle}}</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
        uid:'',
      title: "",
      buttitle: "",
      userlist: [],
      ruleForm: {
        roleid: "",
        username: "",
        password: "",
        status: true
      },
      rules: {
        roleid: [{ required: true, message: "请选择权限", trigger: "blur" }],
        username: [
          { required: true, message: "请输入用户名", trigger: "blur" },
        ],
      },
    };
  },
  mounted() {
    this.id = this.$route.query.id;
    if (this.id) {
      this.title = "用户修改";
      this.buttitle= "修改";
      // this.$http.get('/userinfo',{ uid: this.id }).then(res=>{
      //   console.log(res)
      // })
      console.log(this.id);
      this.$http.get("/userinfo", { uid: this.id }).then((res) => {
        console.log(res);
        let { status } = res.data.list;
        this.ruleForm = {...res.data.list,status : status == 1 ? true : false, password: "",
        };
      });
    } else {
      this.title = "用户添加";
      this.buttitle = "添加";
    }

    this.$http.get("/rolelist", { istree: false }).then((res) => {
      console.log(res);
      this.userlist = res.data.list;
    });
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
              this.ruleForm.status = this.ruleForm.status ? 1 : 2;
          if (this.id) {
            this.$http
              .post("/useredit", { ...this.ruleForm, uid: this.id })
              .then((res) => {
                console.log(res);
              });
          } else {
            this.$http.post("/useradd", this.ruleForm).then((res) => {
              console.log(res);
            });
          }
          this.$router.back();
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
  },
};
</script>

<style lang="stylus" scoped></style>