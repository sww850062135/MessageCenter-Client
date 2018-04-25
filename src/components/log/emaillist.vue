<template>
  <el-row class="warp">
    <el-col :span="24" class="warp-breadcrum" loading="loading">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item to="{path: '/'}"><b>首页</b></el-breadcrumb-item>
        <el-breadcrumb-item>日志</el-breadcrumb-item>
        <el-breadcrumb-item>邮件推送记录</el-breadcrumb-item>
      </el-breadcrumb>
    </el-col>

    <el-col :span="24" class="wrap-main">
     <!-- 功能栏-->
      <el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
        <el-form :inline="true" :model="filters">
          <el-form-item>
            <el-input v-model="filters.subject" placeholder="邮件主题" @keyup.enter.native="handleSearch" style="padding-top: 20px"></el-input>
          </el-form-item>
          <el-form-item style="padding-top: 20px">
            <el-button type="primary" @click="handleSearch">查询</el-button>
          </el-form-item>
          <el-form-item style="padding-top: 20px">
            <el-button type="primary" @click="showSendEmail">新增</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-col>
    <!--列表-->
    <el-table :data="emails" highlightCurrentRow @selection-change="selsChange" height="600" border style="width: 100%;">
      <el-table-column type="selection" width="50"></el-table-column>
      <el-table-column type="index" width="80"></el-table-column>
      <el-table-column prop="email_to" label="收件人" width="200" sortable></el-table-column>
      <el-table-column prop="subject" label="主题" width="300" sortable></el-table-column>
      <el-table-column prop="text" label="内容" width="600" sortable></el-table-column>
      <el-table-column prop="sendtime" label="发送时间" sortable></el-table-column>
    </el-table>
    <!--分页-->
    <el-col :span="24" class="toolbar">
      <el-button type="danger" @click="batchDeleteEmail" :disabled="this.sels.length===0">批量删除</el-button>
      <el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="10" :total="total"
                     style="float:right;">
      </el-pagination>
    </el-col>
  </el-row>
</template>

<script>

  import {getEmailList} from "../../api/api_push"
  import {getEmailListBySubject} from "../../api/api_push"
  export default {
    data(){
      return {
        filters:{
          email_to: '',
          subject: '',
          sendtime: '',
        },
        emails: [],
        total: 0,
        page: 1,
        limit: 10,
        sels: []  //列表选中的列
      }
    },
    methods:{
      handleCurrentChange(val) {
        this.page = val;
        this.search();
      },
      handleSearch(){
        this.total = 0;
        this.page = 1;
        this.search();
      },
      //获取邮件列表
      search: function () {
        let that = this;
        let params = {
          subject: that.filters.subject
        };
        //console.log(params);
        if(params.subject===""){
          getEmailList(params).then(data => {
            let {status, msg, result} = data;
            //console.log(data);
            if (status === "success") {
              that.total = result.total;
              that.emails = result;
            } else {
              that.$message({message: msg, type: 'error'});
            }
          })
        }else {
          getEmailListBySubject(params).then(data => {
            let {status, msg, result} = data;
            //console.log(data);
            if (status === "success") {
              that.total = result.total;
              that.emails = result;
            } else {
              that.$message({message: msg, type: 'error'});
            }
          })
        }

      },
      selsChange: function (sels) {
        this.sels = sels;
      },
      //新增发送邮件
      showSendEmail(){
        this.$router.push({path:'/push/email'});
      },
      //批量删除
      batchDeleteEmail(){

      }
    }
  }
</script>


<style>
</style>
