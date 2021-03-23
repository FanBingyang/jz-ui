<template>
  <div>
    <div>
      <el-button type="primary" size="small" @click="toAdd">新增</el-button>
      <el-input
        v-model="searchData.realName"
        placeholder="输入用户姓名"
        style="width:150px"
        clearable
        size="small"
      ></el-input>
      <!-- <el-input v-model="searchData.gender" placeholder="输入性别" style="width:150px" clearable size="small"></el-input> -->
      <el-select v-model="searchData.gender" placeholder="请选择性别" size="small" clearable>
        <el-option
          v-for="item in genderOptions"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        ></el-option>
      </el-select>
      <el-select v-model="searchData.status" placeholder="请选择状态" size="small" clearable>
        <el-option
          v-for="item in statusOptions"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        ></el-option>
      </el-select>
      <!-- <el-select v-model="searchData.roleId" placeholder="请选择用户类型" size="small" clearable>
                <el-option
                v-for="item in roleOptions"
                :key="item.value"
                :label="item.label"
                :value="item.value">
                </el-option>
      </el-select>-->
      <!-- <el-input v-model="searchData.status" placeholder="输入状态" style="width:150px" clearable size="small"></el-input> -->
      <el-input
        v-model="searchData.telephone"
        placeholder="输入电话"
        style="width:150px"
        clearable
        size="small"
      ></el-input>
      <el-button type="primary" @click="search" size="small">查询</el-button>
    </div>
    <!-- 表格 -->
    <el-table
      :data="customers"
      v-loading="loading"
      element-loading-text="拼命加载中"
      element-loading-spinner="el-icon-loading"
      element-loading-background="rgba(0, 0, 0, 0.8)"
    >
      <el-table-column prop="id" label="编号" />
      <el-table-column prop="realname" label="用户名" />
      <el-table-column prop="gender" label="性别" />
      <!-- <el-table-column label="角色">
            <template slot-scope="scope">
                <p v-if="scope.row.roleId === 1">管理员</p>
                <p v-if="scope.row.roleId === 2">顾客</p>
                <p v-if="scope.row.roleId === 3">员工</p>
            </template>
      </el-table-column>-->
      <el-table-column prop="telephone" label="手机号码" />
      <el-table-column prop="status" label="状态" />
      <el-table-column fixed="right" label="操作">
        <template slot-scope="scope">
          <el-button type="text" @click="edit(scope.row)">编辑</el-button>
          <el-button type="text" @click="del(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 表格结束 -->
    <!-- 模态框 -->
    <el-dialog title="用户" :visible.sync="visable">
      <el-form ref="ruleForm" :model="form" :rules="rules" label-width="80px">
        <el-form-item label="用户名">
          <el-input v-model="form.realname" clearable placeholder="请输入用户名" />
        </el-form-item>
        <el-form-item label="性别" prop="gender">
          <el-select v-model="form.gender" placeholder="请选择性别">
            <el-option label="男" value="男" />
            <el-option label="女" value="女" />
          </el-select>
        </el-form-item>
        <el-form-item label="手机号" prop="telephone">
          <el-input
            v-model="form.telephone"
            clearable
            placeholder="请输入手机号"
            maxlength="11"
            show-word-limit
          />
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="form.password" clearable placeholder="请输入密码" show-password />
        </el-form-item>
        <!-- {{roleId}}
        {{roleOptions[1].label}}-->
        <el-form-item label="类型" prop="roleId">
          <el-select v-model="form.roleId" placeholder="请选择用户类型" size="small" clearable>
            <el-option
              v-for="item in roleOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="状态" prop="status">
          <el-select v-model="form.status" placeholder="请选择状态">
            <el-option label="禁用" value="禁用" />
            <el-option label="启用" value="启用" />
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="close('ruleForm')">取消</el-button>
        <el-button type="primary" @click="submit('ruleForm')">确定</el-button>
      </div>
    </el-dialog>
    <!-- 模态框结束 -->
  </div>
</template>
<script>
import { get, post } from "@/utils/request";
export default {
  data() {
    return {
      text: "",
      visable: false,
      form: {},
      customers: [],
      searchData: {}, //搜索框中的数据
      loading: true,
      genderOptions: [
        {
          value: "男",
          label: "男"
        },
        {
          value: "女",
          label: "女"
        }
      ],
      statusOptions: [
        {
          value: "启用",
          label: "启用"
        },
        {
          value: "禁用",
          label: "禁用"
        }
      ],
      roleOptions: [
        {
          value: "1",
          label: "管理员"
        },
        {
          value: "2",
          label: "顾客"
        },
        {
          value: "3",
          label: "员工"
        }
      ],
      rules: {
        realname: [
          {
            required: true,
            message: "请输入用户名",
            trigger: "blur"
          }
        ],
        status: [
          {
            required: true,
            message: "请选择性别",
            trigger: "change"
          }
        ],
        roleId: [
          {
            required: true,
            message: "请选择用户类型",
            trigger: "change"
          }
        ],
        telephone: [
          {
            required: true,
            message: "请输入手机号",
            trigger: "blur"
          }
        ],
        password: [
          {
            required: true,
            message: "请输入密码",
            trigger: "blur"
          }
        ],
        status: [
          {
            required: true,
            message: "请选择状态",
            trigger: "change"
          }
        ]
      }
    };
  },
  created() {
    this.loadCustomers();
  },
  methods: {
    loadCustomers() {
      let url = "http://121.199.65.223:8888/user/findAllWithCategory";
      get(url).then(response => {
        this.customers = response.data;
        this.loading = false;
      });
    },
    search() {
      this.loading = true;
      // let url = "http://121.199.65.223:8888/user/findExample";
      let url = "http://121.199.65.223:8888/user/findExample";
      let data = Object.assign({}, this.searchData);
      if (data.gender == "") {
        delete data.gender;
      }
      if (data.status == "") {
        delete data.status;
      }
      if (data.roleId == "") {
        delete data.roleId;
      }
      console.log(data);
      post(url, data).then(response => {
        this.customers = response.data;
        this.loading = false;
      });
    },
    toAdd() {
      (this.form = {}), (this.visable = true);
    },
    del(id) {
      this.$confirm("此操作将永久删除该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        // 删除
        let url = "http://121.199.65.223:8888/user/deleteById";
        get(url, {
          id: id
        }).then(response => {
          // 提示
          this.$message({
            type: "success",
            message: response.message
          });
          // 刷新数据
          this.loadCustomers();
        });
      });
    },
    submit(form) {
      this.$refs[form].validate(valid => {
        if (valid) {
          // 表单验证成功的情况
          let url = "http://121.199.65.223:8888/user/saveOrUpdate";
          post(url, this.form).then(response => {
            this.$message({
              type: "success",
              message: response.message
            });
            this.visable = false;
            this.loadCustomers();
            this.$refs[form].resetFields();
          });
        } else {
          return false;
        }
      });
    },
    close(form) {
      this.visable = false;
      this.$refs[form].resetFields();
    },
    edit(row) {
      this.form = row;
      this.visable = true;
    }
  }
};
</script>

<style>
</style>