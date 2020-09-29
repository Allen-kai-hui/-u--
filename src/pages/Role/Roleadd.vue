<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item :to="{ path: '/home/role' }">角色列表</el-breadcrumb-item>
      <el-breadcrumb-item>{{ title }}</el-breadcrumb-item>
    </el-breadcrumb>

    <el-form
      :model="ruleForm"
      :rules="rules"
      ref="ruleForm"
      label-width="100px"
      class="demo-ruleForm"
    >
      <el-form-item label="角色名称" prop="rolename">
        <el-input v-model="ruleForm.rolename"></el-input>
      </el-form-item>
      <el-form-item label="角色权限" prop="menus">
        <el-tree
          :data="data"
          show-checkbox
          node-key="id"
          :default-expanded-keys="[2]"
          :default-checked-keys="checkArr"
          :props="defaultProps"
          ref="roleList"
          check-strictly
        ></el-tree>
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
      title: "",
      buttitle: "",
      menulist: [],
      checkArr:[],
      ruleForm: {
        rolename: "",
        menus: [],
        status: true,
      },
      rules: {
        rolename: [
          { required: true, message: "请输入菜单名称", trigger: "blur" },
        ],
      },
      data: [],
      defaultProps: {
        children: "children",
        label: "title",
      },
    };
  },
  mounted() {
    this.id = this.$route.query.id;
    if (this.id) {
      this.title = "角色修改";
      this.buttitle = "修改";
      this.$http.get("/roleinfo", { id: this.id }).then((res) => {
        console.log(res);
        this.checkArr=res.data.list.menus.split(',')
        let { status } = res.data.list;
        this.ruleForm = {
          ...res.data.list,
          status: status == 1 ? true : false,
        };
      });
    } else {
      this.title = "角色添加";
      this.buttitle = "添加";
    }

    this.$http.get("/menulist", { istree: true }).then((res) => {
      this.data = res.data.list;
    });
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          console.log(this.ruleForm);
          this.ruleForm.status = this.ruleForm.status ? 1 : 2;
          this.ruleForm.menus = this.$refs.roleList.getCheckedKeys();
          console.log(this.ruleForm.menus);
          if (this.id) {
            this.$http
              .post("/roleedit", { ...this.ruleForm, id: this.id })
              .then((res) => {
                console.log(res);
              });
          } else {
            this.$http.post("/roleadd", this.ruleForm).then((res) => {
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