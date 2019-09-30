<template>
  <div>

    <el-form :model="params">
      <el-select v-model="params.siteId" placeholder="请选择站点">
        <el-option v-for="item in siteList" :key="item.siteId" :label="item.siteName" :value="item.siteId"></el-option>
      </el-select>
      页面别名:
      <el-input v-model="params.pageAliase" style="width: 100px"></el-input>
      <el-button type="primary" size="small" v-on:click="query">查询</el-button>
      <router-link :to="{path:'/cms/page/add',query:{
        page:this.params.page,
        siteId:this.params.siteId
      }}">
        <el-button type="primary" size="small">新增</el-button>
      </router-link>
    </el-form>
    <!--列表-->
    <el-table :data="list" stripe style="width: 100%">
      <el-table-column type="index" width="60">
      </el-table-column>
      <el-table-column prop="pageName" label="页面名称">
      </el-table-column>
      <el-table-column prop="pageAliase" label="别名" width="120">
      </el-table-column>
      <el-table-column prop="pageType" label="页面类型" width="150">
      </el-table-column>
      <el-table-column prop="pageWebPath" label="访问路径" width="250">
      </el-table-column>
      <el-table-column prop="pagePhysicalPath" label="物理路径" width="250">
      </el-table-column>
      <el-table-column prop="pageCreateTime" label="创建时间" width="180">
      </el-table-column>
      <el-table-column label="操作" width="180">
        <template slot-scope="page">
          <el-button size="small" type="text" @click="edit(page.row.pageId)">编辑</el-button>
          <el-button size="small" type="text" @click="del(page.row.pageId)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination layout="prev, pager,next" :total="total" :page-size="params.size" :current-page="params.page"
                   style="float: left" @current-change="changePage">

    </el-pagination>
  </div>
</template>

<script>
    import * as cmsApi from "../api/cms";

    export default {
        name: "page_list",
        data() {
            return {
                total: 0,
                params: {
                    page: 1,
                    size: 10,
                    pageAliase: "",
                    siteId: "",
                },
                list: [],
                siteList: [],
            }
        },
        methods: {
            query: function () {
                //调用服务端接口
                cmsApi.page_list(this.params.page, this.params.size, this.params).then((res) => {
                    //将res结果复制给数据模型对象
                    this.list = res.queryResult.list;
                    this.total = res.queryResult.total;
                });
            },
            changePage: function (page) {
                this.params.page = page;
                this.query()
            },
            edit: function (pageId) {
                this.$router.push({
                    path: "/cms/page/edit/" + pageId,
                })
            },
            del:function (pageId) {
                this.$confirm('确认删除吗？', '提示', {}).then(() => {
                    cmsApi.page_del(pageId).then((res)=>{
                        if (res.success ){
                            this.$message.success("删除成功");
                            this.query();
                        }else {
                            this.$message.error("删除失败");
                        }
                    });
                });
            }
        },
        created() {
            this.params.page = Number.parseInt(this.$route.query.page || 1);
        },
        mounted() {
            this.query();
        }
    }
</script>

<style scoped>

</style>
