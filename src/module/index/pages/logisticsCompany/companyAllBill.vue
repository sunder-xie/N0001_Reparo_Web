<template>
  <div class="waybill">
    <el-breadcrumb separator=">" class="bread">
      <svg class="icon combinedShape" aria-hidden="true">   <use xlink:href="#icon-locate"></use> </svg>
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>运单管理</el-breadcrumb-item>
      <el-breadcrumb-item>我的运单</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card>
      <el-tabs v-model="activeName" @tab-click="handleClick">
        <el-tab-pane label="所有运单" name="first">
          <logistics-company-data-table :logisticsCompanyList="allWaybill" status="all" pageSize="10"></logistics-company-data-table>
        </el-tab-pane>
        <el-tab-pane label="发货待确认" name="second">
          <logistics-company-data-table :logisticsCompanyList="allWaybill" status="sendForResponse" pageSize="10"></logistics-company-data-table>
        </el-tab-pane>
        <el-tab-pane label="已发货" name="third">
          <logistics-company-data-table :logisticsCompanyList="allWaybill" status="sended" pageSize="10"></logistics-company-data-table>
        </el-tab-pane>
        <el-tab-pane label="已送达" name="fourth">
          <logistics-company-data-table :logisticsCompanyList="allWaybill" status="received" pageSize="10"></logistics-company-data-table>
        </el-tab-pane>
      </el-tabs>
    </el-card>
  </div>
</template>
<script>
  import store from '../../vuex/store'
  import logisticsCompanyDataTable from '../../components/table/logisticsCompanyDataTable.vue'
  export default {
    name:'index',
    data () {
      return {
//        activeName: 'first',
        allWaybill:[],
      };
    },
    computed:{
      activeName () {
        return store.state.logisticsCompanyTab;
      }
    },
    components:{
      logisticsCompanyDataTable
    },
    methods: {
      handleClick(tab, event){
        console.log(tab, event);
      },
    },
    mounted () {
      document.body.scrollTop = 0;
      document.documentElement.scrollTop = 0;
      this.$http.get("../v1/waybill/allWayBillDetail").then(function(res){
        /*获取企业物流列表*/
        if(res.body.code != 0){
          this.$message.error(res.body.message);
          return;
        }
        this.allWaybill=res.body.data.wayBillDetailVoList;
      },function(err){
        console.log(err);
      });
    }
  }
</script>
