<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>角色列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-button type="primary" @click="roleadd">添加</el-button>

    <el-table
      :data="tableData"
      style="width: 100%;margin-bottom: 20px;"
      row-key="id"
      border
      default-expand-all
      :tree-props="{children: 'children', hasChildren: 'hasChildren'}"
    >
      <el-table-column prop="id" label="编号" width="180"></el-table-column>
      <el-table-column prop="rolename" label="名称" width="180"></el-table-column>
      <el-table-column label="状态">
        <template slot-scope="scope">
          <el-tag>{{ scope.row.status | statusFormat}}</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
          <el-popconfirm title="这是一段内容确定删除吗？" @onConfirm="del(scope.row.id)">
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
      id: "",
    };
  },
  methods: {
    handleEdit(index, row) {
      console.log(index, row);
      this.$router.push("role/edit?id=" + row.id);
    },
    handleDelete(index, row) {
      console.log(index, row);
    },
    roleadd() {
      this.$router.push("role/add");
    },
    del(id) {
      this.$http.post("/roledelete", { id });
      this.getList();
    },
    getList() {
      this.$http.get("/rolelist", { istree: true }).then((res) => {
        console.log(res);
        this.tableData = res.data.list;
      });
    },
  },
  mounted() {
    this.getList();
  },
};
</script>

<style lang="stylus" scoped></style>