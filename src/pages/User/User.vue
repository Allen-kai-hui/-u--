<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
      
    </el-breadcrumb>

    <el-button type="primary" @click="useradd">添加</el-button>

    <el-table
      :data="tableData"
      style="width: 100%;margin-bottom: 20px;"
      row-key="id"
      border
      default-expand-all
      :tree-props="{children: 'children', hasChildren: 'hasChildren'}"
    >
      <el-table-column prop="id" label="编号" width="180"></el-table-column>
      <el-table-column prop="username" label="用户名" width="180"></el-table-column>
      <el-table-column label="所属角色">
                <template slot-scope="scope">
                    <el-tag>{{ scope.row.roleid | roleFormat(roleList)}}</el-tag>
                </template>
            </el-table-column>
      <el-table-column label="状态">
        <template slot-scope="scope">
          <el-tag>{{ scope.row.status | statusFormat}}</el-tag>
        </template>
      </el-table-column>

      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
          <el-popconfirm title="这是一段内容确定删除吗？" @onConfirm="del(scope.row.uid)">
            <el-button slot="reference" size="mini" type="danger">删除</el-button>
          </el-popconfirm>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      visible: false,
      tableData: [],
      roleList:[]
    };
  },
  filters: {
        roleFormat(roleid, roleList) {
            // 根据所有的角色来进行查找满足roleid的那一项
            if (roleList.length > 0) {
                let item = roleList.find(item => {
                return item.id == roleid
            })
            console.log(item)
            return item.rolename;
            }
            
        }
    },
  methods: {
    handleEdit(index, row) {
      console.log(index, row);
      this.$router.push("user/edit?id=" + row.uid);
    },
    handleDelete(index, row) {
      console.log(index, row);
    },
    useradd() {
      this.$router.push("user/add");
    },
    del(uid) {
       
        
      this.$http.post("/userdelete", { uid });
      this.getList();
    },
    getList() {
      this.$http.get("/userlist", { size: 10, page: 1 }).then((res) => {
        console.log(res);
        this.tableData = res.data.list;
      });
    },
  },
  mounted() {
    this.getList();
    this.$http.get("/rolelist").then(res => {
            this.roleList = res.data.list
        })
  },
};
</script>

<style lang="stylus" scoped></style>