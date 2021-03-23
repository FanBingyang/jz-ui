<template>
    <div class="category">

        <!-- 按钮 -->
        <div>
            <el-button type="primary" @click="toAdd" size="small">新增</el-button>
            <el-input v-model="input" placeholder="输入父栏目名称" style="width:200px" clearable size="small"></el-input>
            <el-button type="primary" @click="loadCategorysWithChild" size="small">查询</el-button>
        </div>

        <!-- 表格 -->
        <el-table :data="categoryWithChild" v-loading="loading"
            element-loading-text="拼命加载中"
            element-loading-spinner="el-icon-loading"
            element-loading-background="rgba(0, 0, 0, 0.8)"
            row-key="id"
            :tree-pops="{children: 'children', hasChildren: 'hasChildren'}"
            >

            <el-table-column label="编号" type="index" :index="1" width="180"></el-table-column>
            <el-table-column label="栏目名称" prop="name"></el-table-column>
            <el-table-column label="序号" prop="nu"></el-table-column>
            <el-table-column label="操作">
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
        <el-form-item label="栏目名称：">
            <el-input v-model="formData.name" autocomplete="off" clearable></el-input>
        </el-form-item>
        <el-form-item label="栏目序号：">
            <el-input v-model="formData.nu" autocomplete="off" clearable></el-input>
        </el-form-item>
        <el-form-item label="所属栏目：">
            <!-- <el-select v-model="formData.parentId" placeholder="选择所属栏目">
                <el-option v-for="c in categorys" :key="c.id" :label="c.name" :value="c.id"></el-option>
            </el-select> -->

            <el-cascader placeholder="选择所属栏目" v-model="formData.parentId" :options="categoryWithChild" :props="{ label:'name',value:'id',checkStrictly: true,emitPath:false }" clearable></el-cascader>
        </el-form-item>

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
    import {get,
        post
    } from '@/utils/request';
    export default {
        data() {
            return {
                categorys: [], // 全部栏目            
                categoryWithChild: [], // 联级栏目数据
                loading: true, // 加载表格数据动画的初始状态
                dialogTitle: '添加栏目', // 模态框标题
                dialogFormVisible: false, // 模态框是否可见
                formData: [], // 表单数据
                input: '', // 查询输入框中的值
            }
        },
        created() {
            this.loadCategorysWithChild();
            this.loadCategory();
        },

        methods: {
            // 加载级联栏目
            loadCategorysWithChild() {
                let url = "http://121.199.65.223:8888/category/findAllWithChild";
                let data = {
                    name: this.input
                }
                get(url, data).then((res) => {
                    if (res.status == 200) {
                        // 为页面数据赋值  
                        this.categoryWithChild = res.data;
                        // 关闭加载动画
                        this.loading = false;
                    } else {
                        this.$message.error(res.message);
                    }
                });
            },

            // 加载所有栏目
            loadCategory() {
                let url = "http://121.199.65.223:8888//category/findAll";
                get(url).then((response) => {
                    if (response.status == 200) {
                        // 为页面数据赋值  
                        this.categorys = response.data;
                    } else {
                        this.$message.error(res.message);
                    }
                });
            },

            // 点击添加新得产品
            toAdd() {
                // 设置模态框标题
                this.dialogTitle = "添加栏目";
                // 清空模态框数据
                this.formData = {};
                // 设置模态框可见
                this.dialogFormVisible = true;
            },

            // 修改编辑
            edit(p) {
                // 设置模态框标题
                this.dialogTitle = "编辑栏目"
                    // 给模态框数据赋值
                this.formData = Object.assign({}, p);
                // 设置模态框可见
                this.dialogFormVisible = true;
            },

            // 提交表单
            submit() {
                let url = "http://121.199.65.223:8888/category/saveOrUpdate";
                // 获取表单数据
                let data = this.formData
                    // 提交
                post(url, data).then((res) => {
                    if (res.status == 200) {

                        // 提示信息
                        this.$message({
                            message: res.message,
                            type: 'success'
                        });
                        // 关闭模态框
                        this.dialogFormVisible = false;
                        //  加载产品信息
                        this.loadCategorysWithChild();
                        // 清空模态框数据
                        this.formData = {};
                    } else {
                        this.$message.error(res.message);
                    }
                })
            },


            // 删除
            del(pid) {
                this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    // 删除
                    let url = "http://121.199.65.223:8888/category/deleteById";
                    let data = {
                        id: pid
                    };
                    get(url, data).then((res) => {
                        // 提示
                        this.$message({
                                type: 'success',
                                message: res.message
                            })
                            // 刷新数据
                        this.loadCategorysWithChild();
                    })
                });
            },

        }
    }
</script>