<template>
    <div>
        <div class="search_form">
            <el-form ref="search_form" :model="search_form">
                <el-form-item class="top_search">
                    <el-input v-model="search_form.content" class="search_input" placeholder="搜索名称"></el-input>
                </el-form-item>
                <el-form-item class="top_search">
                    <el-button type="success" @click="searchHandle">搜索</el-button>
                </el-form-item>
            </el-form>
        </div>
        <div class="top-btn">
            <el-button type="primary" @click="linkToDetail('0')">添加</el-button>
        </div>
        <el-table :data="tableData" border style="width: 100%">
            <el-table-column prop="id" label="作品序号" sortable>
            </el-table-column>
            <el-table-column prop="content" label="投票作品数据内容">
            </el-table-column>
            <el-table-column prop="imgUrls" label="作品图片">
            </el-table-column>
            <el-table-column label="操作">
                <template scope="scope">
                    <el-button size="small" @click="handleEdit(scope.row.id)">编辑</el-button>
                    <el-button size="small" type="danger" @click="handleDelete(scope.row.id)">删除</el-button>
                </template>
            </el-table-column>
        </el-table>
        <div class="pagination">
            <el-pagination  @size-change="handleSizeChange" @current-change="handleCurrentChange"
                            :pageSize="pageSum" :page-sizes="[2,4,6,8,10,12,14]" layout="total,sizes, prev, pager, next, jumper"  :total="sum">
            </el-pagination>
        </div>
    </div>
</template>
<style type="text/css">
    .search_input {
        width: 150px!important;
    }
</style>
<script>
    import {
        MessageBox
    } from 'element-ui';
    export default {
        data() {
            return {
                search_form: {
                    content: ''
                },
                tableData: [],
                pageSum: 10,
                sum: 0,
                cur_page: 1
            }
        },
        watch: {

        },
        mounted() {
            this.getData();
        },
        methods: {
            linkToDetail(id) {
                if (id == 0)
                    this.$router.push("/vote-data-list/detail/0");
                else
                    this.$router.push("/vote-data-list/detail/" + id);
            }, handleSizeChange(val) {
                this.pageSum=val;
                this.getData();
            },
            handleCurrentChange(val) {
                this.cur_page = val;
                this.getData();
            },
            getData() {
                const self = this;
                self.$axios({
                    url: '/vote-data/list',
                    method: 'get',
                    params: {
                        content:self.search_form.content,
                        curPage: self.cur_page,
                        pageSum: self.pageSum
                    }
                })
                    .then((res) => {
                        if (res != null) {
                            self.tableData = res.data.list;
                            self.sum = res.data.sum;
                        }
                    })
            },
            searchHandle() {
                this.getData();
            },
            handleDelete(id) {
                const self = this;
                MessageBox.confirm('确定删除？').then(function(action) {
                    self.$axios({
                        url: '/vote-data/delete',
                        method: 'post',
                        data: {
                            id: id
                        }
                    })
                        .then((res) => {
                            if (res.data==true) {
                                self.$message("删除成功");
                                self.getData();
                            }
                        })
                }).catch(function() {
                    console.log("取消投票作品数据删除");
                });
            },
            handleEdit(id) {
                this.linkToDetail(id);
            }
        }
    }
</script>
