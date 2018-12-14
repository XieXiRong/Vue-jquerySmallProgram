<template>
  <div class="content" v-loading="loading">
    <div class="left_list">
      <div @click="showmsite" :class="mysroutine?'active':''">
        <i class="iconfont icon-bussiness"></i>
        <span>我的小程序</span>
      </div>
      <div @click="manager" :class="showlist?'active':''" v-if="showManager">
        <i class="iconfont icon-guanli"></i>
        <span>小程序管理</span>
      </div>
      <div v-if="showlist" @click="waits" class="item5" :class="waited?'active':''">
        <el-badge :value="waitCount" class="item" :class="waited?'active':''">
          <span>待审批</span>
        </el-badge>
      </div>
      <div v-if="showhelp" @click="helpshow" :class="showHelpList||fromHelp?'active':''">
        <el-badge :value="AssistMPList.length" class="item item2" :class="showHelpList||fromHelp?'active':''">
          <i class="iconfont icon-xiezhuqingqiu"></i>
          <span>&nbsp;协助审核</span>
        </el-badge>
      </div>
    </div>
    <div class="middle_list">
      <div class="mhead">
        <span v-html="head1">小程序列表</span>
      </div>
      <HelpList v-if="showHelpList" :MPAttachments="MPAttachments" :tableData="AssistMPList" @openHelpView="openHelpView"/>
      <Manager v-if="showlist" :waited="waited" :tableData="MPList" @waitCountNum="waitCountNum" @openSrManager="openSrManager"/>
    </div>
    <SroutineDetail :imgsrc="imgsrc" :showoneData="showoneData" :MPAttachments="MPAttachments" :fromHelp="fromHelp" v-if="showone" @closeone="closeone" @addnewbusiness="addnewbusiness"
                    @addApplyplan="addApplyplan" @showTestSubmit="showTestSubmit" @openOnlineSubmit="openOnlineSubmit"/>
    <Applynew v-if="showapply" @applyaddSuccessed="applyaddSuccessed" @closeapplyadd="closeapplyadd"/>
    <keep-alive><Msitelist v-if="showm" :MyMPList="MyMPList" :MPAttachments="MPAttachments" :showapply="showapply" @showapplynew="showapplynew" :applyaddSuccess="applyaddSuccess" @showOne="showOne"/></keep-alive>
    <Applynewbusiness v-if="showaddbusiness" @closenewbusiness="closenewbusiness" :addBusinessData="addBusinessData"/>
    <Applyplan v-if="showApplyplan" @closeApplyplan="closeApplyplan" :apllyPlan="apllyPlan"/>
    <TestSubmit v-if="showTestSubmiton" :testSubmit="testSubmit" @closeTestSubmit="closeTestSubmit"/>
    <SroutineManager :MPAttachments="MPAttachments" :AssistMPList="AssistMPList" v-if="showSrManager" :showSrManagerInfo="showSrManagerInfo" @closeSrManager="closeSrManager"/>
    <OnlineSubmit v-if="onlineSub" :onlineData="onlineData" @closeOnlineSubmit="closeOnlineSubmit"/>
  </div>
</template>

<script>
  import Rightbar from '../../components/Rightbar'
  import Msitelist from '../../components/Msitelist'
  import Manager from '../../components/Manager'
  import Applynew from '../../components/Applynew'
  import SroutineDetail from '../../components/SroutineDetail2'
  import Applynewbusiness from '../../components/Applynewbusiness'
  import Applyplan from '../../components/Applyplan'
  import TestSubmit from '../../components/TestSubmit'
  import SroutineManager from '../../components/SroutineManager'
  import OnlineSubmit from '../../components/OnlineSubmit'
  import HelpList from '../../components/HelpList'
  export default {
    data() {
      return {
        admin:true,
        showlist: false,
        showm: true,
        mysroutine:true,
        waited: false,
        restaurants: [],
        state1: '',
        showapply:false,
        applyaddSuccess:{},
        showoneData:'',
        showone:false,
        showaddbusiness:false,
        showApplyplan:false,
        showTestSubmiton:false,
        waitCount:0,
        showManager:false,
        showSrManager:false,
        showSrManagerInfo:{},
        onlineSub:false,
        showHelpList:false,
        head1:'',
        fromHelp:false,  //判断页面是否从协助审查过来的
        showhelp:true,
        addBusinessData:{},
        MyMPList:[],
        MPList:[],
        AssistMPList:[],
        apllyPlan:[],
        testSubmit:{},
        MPAttachments:{},
        imgsrc:'',
        loading:true,
        onlineData:{},
      }
    },
    props:{

    },
    components:{
      Manager:Manager,
      Rightbar:Rightbar,
      Msitelist:Msitelist,
      Applynew:Applynew,
      SroutineDetail:SroutineDetail,
      Applynewbusiness:Applynewbusiness,
      Applyplan:Applyplan,
      TestSubmit:TestSubmit,
      SroutineManager:SroutineManager,
      OnlineSubmit:OnlineSubmit,
      HelpList:HelpList
    },
    mounted(){
        if(this.admin==true){
          this.showManager = true
        }
      var CurUser = JSON.parse(localStorage.getItem("CurUser"));
      var vm =this
      $.ajax({
        url: vm.User.baseUrl + "/api/miniprogram?userId=" + CurUser.USID,
        type: 'Get',
        beforeSend: function (xhr) {
          xhr.setRequestHeader('Authorization', 'BasicAuth ' + CurUser.TICKET)
        },
        xhrFields: {
          withCredentials: true
        },
        crossDomain: true,
        success: function (result) {
          console.log(result)
          if(result.StateCode){
            var data = result.Data;
            vm.loading = false
            vm.AssistMPList = data.AssistMPList
            vm.MPList = data.MPList
            vm.MyMPList =data.MyMPList
            vm.MPAttachments = data.MPAttachments

            console.log(data)
          }
        },
        error: function (XMLHttpRequest, textStatus, errorThrown) {
          console.log("err")
        },
        cache: false,
        dataType: 'json'
      });

      //设置用户权限
      // if(user.ROLETYPE==0){
      //     this.showManager= false
      // }else if(user.ROLETYPE==1){
      //   this.showManager= true
      // }else if(user.ROLETYPE==2){
      //   this.showManager= false
      //     this.showhelp = true
      // }
    },

    watch: {

    },

    methods: {
      openHelpView(item,src){
        this.showHelpList = false
        this.showone = true
        this.showoneData = item
        this.fromHelp = true
        this.imgsrc = src
      },
      closehelpshow(){
        this.showHelpList = false
      },
      helpshow(){
        this.showHelpList = true
        this.head1 = '协助审核'
        this.showm = false
        this.showlist = false
        this.mysroutine = false
      },
      openOnlineSubmit(item){
        this.onlineSub = true
        this.onlineData = item
      },
      closeOnlineSubmit(){
        this.onlineSub = false
      },
      closeSrManager(){
        this.showSrManager = false
      },
      openSrManager(item){
        this.showSrManager = true
        this.showSrManagerInfo = item
      },
      waitCountNum(num){
        this.waitCount = num
      },
      showTestSubmit(item){
        this.showTestSubmiton = true
        this.testSubmit = item
      },
      closeTestSubmit(){
        this.showTestSubmiton = false
      },
      addApplyplan(item){
        this.showApplyplan = true
        this.apllyPlan = item
      },
      closeApplyplan(){
        this.showApplyplan = false
      },
      closenewbusiness(){
        this.showaddbusiness = false
      },
      addnewbusiness(item){
         this.showaddbusiness = true
        this.addBusinessData = item
      },
      closeone1(){
        this.showone1 = false
        this.showm = true
      },
      closeone(showoneData){
        if(this.fromHelp){
          this.showHelpList = true
          this.showone = false
          this.showm = false
        }else{
          this.showm = true
          this.showone = false
          this.showHelpList = false
          //this.showoneData
        }
      },
      showOne(item,index,src){
        this.showoneData = item
        this.showone = true
        this.showm = false
        this.mysroutine = true
        this.imgsrc = src
      },
      applyaddSuccessed(data){
          this.applyaddSuccess = data
          this.showm = true
          this.showapply = false
      },
      closeapplyadd(){
        this.showm = true
        this.showapply = false
      },
      showapplynew(){
        this.showapply = true
        this.showlist = false
        this.showm = false
      },
      waits() {
        this.waited = !this.waited
        this.showSrManager = false
        this.showHelpList = false
      },
      manager() {
        this.closeSrManager()
        this.showone = false
        this.showlist = true
        this.showm = false
        this.showapply = false
        this.waited = false
        this.showHelpList = false
        this.mysroutine = false
        this.head1 = '小程序列表'
        this.fromHelp = false
      },
      showmsite() {
        this.closeSrManager()
        this.showlist = false
        this.showm = true
        this.fromHelp = false
        this.showapply = false
        this.showHelpList = false
        this.mysroutine = true
      },
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .content {
    position: relative;
    padding-top: 40px;
    height: 820px;
    width: 1440px;
    margin: 0 auto;
  }

  .left_list {
    position: absolute;
    top: 0;
    left: 25px;
    height: 820px;
    width: 230px;
    background-color: rgba(248, 248, 248, 1);
    text-align: center;
    border: 1px solid rgba(0, 0, 0, 0.05);

  }

  .item {
    width: 30px;
    height: 20px;
    background-color: inherit;
    text-align: center;
  }
  .left_list div {
    text-align: center;
    width: 230px;
    height: 60px;
    color: #101010;
    border-bottom: 1px solid rgba(0, 0, 0, 0.05);
    background-color: white;
    cursor: pointer;
  }
  .left_list .item5{
    height: 45px;
    border-bottom: 1px solid rgba(0,0,0,0.05);
  }
  .left_list div span {
    line-height: 61px;
    font-size: 16px;
    font-family: SourceHanSansSC-regular;
  }
  .left_list .item5 span{
    line-height: 45px;
  }
  .left_list div i {
    line-height: 60px;
    font-size: 24px;
  }

  .left_list .active {
    background-color: #f56c6c;
    color: black;
  }

  .middle_list {
    top: 0;
    right: 25px;
    position: absolute;
    height: 820px;
    width: 1130px;
    border: 1px solid rgba(187, 187, 187, 1);
    background-color: #fff;
  }

  .mhead {
    background-color: #f8f8f8;
    height: 60px;
    text-align: left;
  }

  .mlist .searchs {
    width: 1054px;
    height: 94px;
    border: none;
  }

  .mlist .searchs div {
    border: none;
    position: absolute;
    right: 20px;
  }
.searchs{
  position: absolute;
  right: 70px;
  top:130px;
}
.left_list .icon-xiezhu{
  line-height: 60px;
  font-size: 30px;

}
  .mlist .el-select {
    margin: 25px 0;

  }
  .searchs span{
    position: relative;
    right: 20px;
    display: inline-block;
  }
  .el-input {
    margin-bottom: 20px;
  }

  .mhead span {
    margin-left: 45px;
    line-height: 60px;

    font-size: 20px;
    color: #101010;
  }

  .mlist div {
    margin: 40px;
    display: inline-block;
    float: left;
    width: 200px;
    height: 238px;
    line-height: 20px;
    text-align: center;
    border: 1px solid rgba(187, 187, 187, 1);
  }

  .mlist div img {
    margin: 35px 0 10px 0;
    width: 100px;
    height: 100px;
  }

  .mlist .mname {
    font-size: 20px;
    color: #101010;
    margin-bottom: 10px;
    font-family: SourceHanSansSC-regular;
  }

  .mlist .mmessage {
    font-size: 16px;
    color: #101010;
  }

  .mlist .madd {
    border: 1px dashed rgba(187, 187, 187, 1);

  }

  .mlist .madd i {
    font-size: 100px;
    color: rgba(16, 16, 16, 0.44);
    line-height: 238px;
  }

  .content .active2 {
    background-color: #FFA700;
    color: white;
  }
  .content .active3{
  background-color: lightseagreen;
  color: white;
}
</style>

<style>
   #app .middle_list{
    border: 1px solid rgba(0, 0, 0, 0.05);
  }
   #app .mhead{
    border-bottom: 1px solid rgba(0, 0, 0, 0.05);
  }
</style>
