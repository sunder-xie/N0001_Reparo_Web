<template>
  <div  >
    <el-breadcrumb separator=">" class="bread">
      <svg class="icon combinedShape" aria-hidden="true">   <use xlink:href="#icon-locate"></use> </svg>
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>仓储管理</el-breadcrumb-item>
      <el-breadcrumb-item>仓储详情</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card>
      <el-row class="el-row-header statePosition">
        <el-col class="detail_title_color stateShow"><svg class="icon" aria-hidden="true">   <use xlink:href="#icon-zhuangtai"></use> </svg>
          仓储当前状态:{{repoDetails.curRepoBusiStatus | repoStatus}}</el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-card class="box-card mybox" style="width:100%">
            <div slot="header" class="clearfix el-row-header">
              <svg class="icon detailIcon" aria-hidden="true">   <use xlink:href="#icon-cc_H"></use> </svg>
              <span class="keynote">仓储信息</span>
            </div>
            <div class="box-card mycard1 detailContent">
              <el-row>
                <el-col :span="8" class="msgName keynote">仓储业务编号：{{repoDetails.repoBusiNo}}</el-col>
                <el-col :span="8" class="msgName">仓储状态：{{repoDetails.curRepoBusiStatus | repoStatus}}</el-col><!--卖家和买家会显示混合-->
                <el-col :span="8" class="msgName" v-if="state.isBuyer==='true'">入库时间：{{repoDetails.inRepoTime | timeTransfer}}</el-col><!--筛选-->
                <el-col :span="8" class="msgName" v-else>出库时间：{{repoDetails.outRepoTime | timeTransfer}}</el-col><!--筛选-->
              </el-row>
              <el-row>
                <el-col :span="8" class="msgName keynote" v-if="repoDetails.repoCertNo===''">仓单编号：暂无</el-col>
                <el-col :span="8" class="msgName keynote" v-else>仓单编号：{{repoDetails.repoCertNo}}</el-col>
                <el-col :span="8" class="msgName">仓储机构：{{repoDetails.repoEnterpriceName}}</el-col>
              </el-row>
              <el-row>
                <el-col :span="8" class="msgName">货品名称：{{repoDetails.productName}}</el-col>
                <el-col :span="8" class="msgName">货品数量：{{repoDetails.productQuantity}}</el-col>
                <el-col :span="8" class="msgName">货品数量：{{repoDetails.productQuantity}}</el-col>
                <el-col :span="8" class="msgName">货品总额(元)：{{repoDetails.productTotalPrice}}</el-col>
              </el-row>
              <el-row>
                <el-col :span="8" class="msgName">物流公司：{{repoDetails.logisticsEntepsName | nullSituation}}</el-col>
                <el-col :span="8" class="msgName">物流运单号：{{repoDetails.waybillNo | nullSituation}}</el-col>
              </el-row>
              <el-row>
                <el-col :span="8" class="msgName">仓储状态明细：</el-col>
              </el-row>

              <el-row class="collapseTop">
                <template v-for="(item,index) in repoDetails.operationRecordVoList">
                  <el-row class="status-list" :class="{circleColor:index==(repoDetails.operationRecordVoList.length-1)}">
                    <el-col :span="24" :class="{circleColor1:index==(repoDetails.operationRecordVoList.length-1)}"><span>{{item.state | repoStatus}}：{{item.operateTime | timeTransfer}}</span></el-col>
                  </el-row>
                </template>
              </el-row>

            </div>
          </el-card>
        </el-col>
      </el-row>
    </el-card>
  </div>
</template>
<script>
  import store from '../../vuex/store'
  import constantData from '../../../../common/const'
  export default {
    name:'index',
    data () {
      return {
        repoDetails:{
          serialNumber:'20170403123456',
          state:'已入库',
          storageTime:'2017-08-01',
          operationRecordVoList:[],
          inRepoTime:'',
          outRepoTime:''
        }
      }
    },
    computed:{
      state () {
          return store.state
      }
    },
    mounted () {
      document.body.scrollTop = 0;
      document.documentElement.scrollTop = 0;
        this.$http.get("../v1/repository/getRepoBusiHistoryList?repoBusinessNo="+store.state.checkIdRepo).then(function(res){
//            请求仓储详情数据
          if(res.body.code != 0){
            this.$message.error(res.body.message);
            return;
          }
          this.repoDetails=res.body.data;
          this.repoDetails.inRepoTime='';//返回数据里面没有这两个字段
          this.repoDetails.outRepoTime='';
          for(var item in this.repoDetails.operationRecordVoList){
              var temp=this.repoDetails.operationRecordVoList[item];
              if(temp.state===constantData.ALREADYIN){/*筛选入库时间*/
                console.log("筛选入库时间");
                  this.repoDetails.inRepoTime=temp.operateTime;
                  break;
              }
          }
          for(var item in this.repoDetails.operationRecordVoList){
              var temp=this.repoDetails.operationRecordVoList[item];
              if(temp.state===constantData.ALREADYOUT){/*筛选出库时间*/
                console.log("筛选出库时间");
                  this.repoDetails.outRepoTime=temp.operateTime;
                  break;
              }
          }
        },function(err){
            console.log(err)
        });
    }
  }
</script>
