<template>
    <div>
      <!-- 表格 -->
      <el-table :data="comments"
            v-loading="loading"
            element-loading-text="拼命加载中"
            element-loading-spinner="el-icon-loading"
            element-loading-background="rgba(0, 0, 0, 0.8)">
        <el-table-column prop="id" label="编号" />
        <el-table-column prop="score" label="评分" />
        <el-table-column prop="content" label="内容" />
        <el-table-column prop="commentTime" label="评价时间" />
        <el-table-column prop="orderId" label="订单ID" />
        <el-table-column prop="status" label="状态" />
        <el-table-column fixed="right" label="操作">
          <template slot-scope="scope">
            <el-button type="text" @click="del(scope.row.id)">删除</el-button>
            <el-button type="text" @click="refuse(scope.row.id)">拒绝审核</el-button> 
          </template>
</el-table-column>
</el-table>
<!-- 表格结束 -->
</div>
</template>
<script>
    import {get,
        post
    } from "@/utils/request";
    export default {
        data() {
            return {
                text: '',
                visable: false,
                form: {},
                comments: []
            };
        },
        created() {
            this.loadComments();
        },
        methods: {
            loadComments() {
                let url = "http://121.199.65.223:8888/comment/findAll";
                get(url).then(response => {
                    this.comments = response.data;
                });
            },
            del(id) {
                this.$confirm("此操作将永久删除该数据, 是否继续?", "提示", {
                    confirmButtonText: "确定",
                    cancelButtonText: "取消",
                    type: "warning"
                }).then(() => {
                    // 删除
                    let url = "http://121.199.65.223:8888/comment/deleteById";
                    get(url, {
                        id: id
                    }).then(response => {
                        // 提示
                        this.$message({
                            type: "success",
                            message: response.message
                        });
                        // 刷新数据
                        this.loadComments();
                    });
                });
            },
            close(form) {
                this.visable = false;
                this.$refs[form].resetFields();
            },
            refuse(id) {
                let url = "http://121.199.65.223:8888/comment/refuseAuditing";
                get(url, {
                    id: id
                }).then(response => {
                    this.$message({
                        type: "success",
                        message: response.message
                    });
                    this.loadComments();
                });
                this.visible = false;
            }

        }
    };
</script>

<style>

</style>