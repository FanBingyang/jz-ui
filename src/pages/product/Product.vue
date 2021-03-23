<template>
  <div class="product">
    <!-- 按钮/查询 -->
    <div>
      <el-button type="primary" @click="toAdd" size="small">新增</el-button>
      <el-input v-model="input" placeholder="输入产品名称" style="width:200px" clearable size="small"></el-input>
      <el-button type="primary" @click="loadProducts" size="small">查询</el-button>
    </div>
    <!-- /按钮 -->

    <!-- 表格 -->
    <el-table
      :data="products"
      v-loading="loading"
      element-loading-text="拼命加载中"
      element-loading-spinner="el-icon-loading"
      element-loading-background="rgba(0, 0, 0, 0.8)"
    >
      <el-table-column label="编号" type="index" align="center" :index="1"></el-table-column>

      <el-table-column label="图片" width="80">
        <template v-slot="scope">
          <img
            style="width:50px; height:50px; border-radius:3px"
            :src="'http://121.199.29.84:8888/'+scope.row.photo"
            alt
          />
        </template>
      </el-table-column>

      <el-table-column label="名称" prop="name" width="120"></el-table-column>
      <el-table-column label="单价" prop="price" width="60"></el-table-column>
      <el-table-column label="所属分类" prop="category.name" width="120"></el-table-column>
      <el-table-column label="介绍" prop="introduce"></el-table-column>
      <el-table-column label="操作" width="160">
        <template slot-scope="scope">
          <el-button @click="edit(scope.row)" type="text">编辑</el-button>
          <el-button type="text" @click="del(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <!-- 模态框 -->
    <el-dialog :visible.sync="dialogFormVisible" @close="formData={}" width="50%">
      <!-- 标题内容 -->
      <div slot="title" class="dialog-title">
        <h3>{{dialogTitle}}</h3>
      </div>
      <el-form :mode="formData" label-width="120px">
        <el-form-item label="产品名称：">
          <el-input v-model="formData.name" autocomplete="off" clearable></el-input>
        </el-form-item>
        <el-form-item label="产品单价：">
          <el-input v-model="formData.price" autocomplete="off" clearable></el-input>
        </el-form-item>
        <el-form-item label="所属栏目：">
          <!-- <el-select v-model="formData.categoryId" placeholder="选择所属栏目">
                        <el-option v-for="c in categorys" :key="c.id" :label="c.name" :value="c.id"></el-option>
          </el-select>-->

          <el-cascader placeholder="选择所属栏目"
            v-model="formData.categoryId"
            :options="categorys"
            :props="{ label:'name',value:'id',checkStrictly: true,emitPath:false }"
            clearable
          ></el-cascader>
        </el-form-item>
        <el-form-item label="产品描述：">
          <el-input type="textarea" v-model="formData.introduce" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="产品状态：">
          <el-input v-model="formData.status" autocomplete="off" clearable></el-input>
        </el-form-item>
        <el-form-item label="产品图片：">
          <el-upload
            class="upload-demo"
            :multiple="false"
            :limit="1"
            :on-success="uploadSuccessHandler"
            :on-error="uploadErrorHandler"
            action="http://121.199.29.84:5588/file/upload"
            list-type="picture"
          >
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
          </el-upload>

          <!-- <el-input v-model="formData.photo" autocomplete="off" clearable></el-input> -->
        </el-form-item>
        <el-form-item label="产品销量：">
          <el-input v-model="formData.sales" autocomplete="off" clearable></el-input>
        </el-form-item>
        <!-- <el-form-item label="产品类别："><el-input v-model="formData.categoryId" autocomplete="off" clearable></el-input></el-form-item> -->
      </el-form>
      <!-- 底部内容 -->
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="submit">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { get, post } from "@/utils/request";
export default {
  data() {
    return {
      name: "产品管理",
      dialogTitle: "添加产品", // 模态框初始标题
      dialogFormVisible: false, // 模态框初始状态(不显示)
      formData: {}, // 模态框数据
      products: [], // 初始化，产品数据
      loading: true, // 加载表格数据动画的初始状态
      categorys: [], // 栏目列表
      input: "" // 查询输入框中的值
    };
  },
  // 表示vue实例创建完毕，开始进行数据渲染
  created() {
    // 加载产品信息
    this.loadProducts();
    this.loadCategory();
  },

  methods: {
    // 加载产品信息
    loadProducts() {
      let url = "http://121.199.65.223:8888/product/findAllWithCategory";
      let data = { name: this.input };
      get(url, data).then(res => {
        if (res.status == 200) {
          // 为页面数据赋值
          this.products = res.data;
          // 关闭加载动画
          this.loading = false;
        } else {
          this.$message.error(res.message);
        }
      });
    },

    // 加载所有级联栏目
    loadCategory() {
      let url = "http://121.199.65.223:8888/category/findAllWithChild";
      get(url).then(response => {
        if (response.status == 200) {
          // 为页面数据赋值
          this.categorys = response.data;
        } else {
          this.$message.error(res.message);
        }
      });
    },

    // 提交表单
    submit() {
      let url = "http://121.199.65.223:8888/product/saveOrUpdate";
      // 获取表单数据
      let data = this.formData;
      // 提交
      post(url, data).then(res => {
        if (res.status == 200) {
          // 提示信息
          this.$message({
            message: res.message,
            type: "success"
          });
          // 关闭模态框
          this.dialogFormVisible = false;
          //  加载产品信息
          this.loadProducts();
          // 清空模态框数据
          this.formData = {};
        } else {
          this.$message.error(res.message);
        }
      });
    },

    // 点击添加新得产品
    toAdd() {
      // 设置模态框标题
      this.dialogTitle = "添加产品";
      // 清空模态框数据
      this.formData = {};
      // 设置模态框可见
      this.dialogFormVisible = true;
    },

    // 修改编辑
    edit(p) {
      // 设置模态框标题
      this.dialogTitle = "编辑产品";
      // 给模态框数据赋值
      this.formData = Object.assign({}, p);
      // 设置模态框可见
      this.dialogFormVisible = true;
    },

    // 删除
    del(pid) {
      this.$confirm("此操作将永久删除该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        // 删除
        let url = "http://121.199.65.223:8888/product/deleteById";
        let data = { id: pid };
        get(url, data).then(res => {
          // 提示
          this.$message({ type: "success", message: res.message });
          // 刷新数据
          this.loadProducts();
        });
      });
    },

    // 上传成功
    uploadSuccessHandler(res) {
      // console.log(res);
      let photo = res.data.groupname + "/" + res.data.id;
      // 下面两个等价
      // this.formData.photo = photo;
      // 拿出原有数据，添加新的重新复制。formData的引用地址发生改变
      this.formData = { ...this.formData, photo };
    },

    // 上传失败
    uploadErrorHandler(error) {
      this.$message({ type: "error", message: "上传失败" });
    }
  }
};
</script>
