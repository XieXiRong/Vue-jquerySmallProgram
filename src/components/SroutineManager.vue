<template>
  <div class="middle_list">
    <div class="mhead">
      <span>小程序详情</span>
      <i class="iconfont icon-fanhui1" @click="goback1"></i>
    </div>
    <div class="wrap">
      <div class="head_detail">
        <div class="detail_logo">
          <img :src="imgUrl?User.baseUrl+imgUrl.RealValue:'../../static/images/Image1.png'" alt="">
        </div>
        <div class="detail_des">
          <div class="name">
            <span>{{showSrManagerInfo.PApplyFullName}}</span>
            <span class="state"  :class="{'stateok':showSrManagerInfo.CurrStateNo%2==0,
             'statenotok':showSrManagerInfo.CurrStateNo==13||showSrManagerInfo.CurrStateNo==23||showSrManagerInfo.CurrStateNo==33||showSrManagerInfo.CurrStateNo==43||showSrManagerInfo.CurrStateNo==53}">{{showSrManagerInfo.CurrStateName}}</span>
            <span class="onlineNow1" @click="editDetail">编辑</span>
            <span class="onlineNow3" @click="saveEditDetail">保存</span>
          </div>
          <div class="bankname">
            <span>{{showSrManagerInfo.PUserBranch}}</span>
            <span>{{showSrManagerInfo.PMerchantType}}</span>
          </div>
          <div class="des">
            <p>{{showSrManagerInfo.PDesc}}</p>
          </div>
        </div>
      </div>
      <div class="action">
        <el-row>
          <el-button type="success" v-if="managerButton" @click="nextapply1">同意申请</el-button>
          <el-button type="danger" v-if="managerButton" @click="nextapply2">拒绝申请</el-button>
          <el-button type="danger" v-if="managerButton&&resendButton" @click="nextapply3">转发</el-button>
        </el-row>
      </div>
      <DetailList :showSrManagerInfo="showSrManagerInfo" :showDetail="showDetail1" @saveEditDetail="saveEditDetail" :MPAttachments="MPAttachments"/>
    </div>
    <ApplyAgree v-if="showApplyAgree" :ApplyAgreeTitle="ApplyAgreeTitle" :showSrManagerInfo="showSrManagerInfo" :type="type" @closeApply="closeApply"/>
    <ResendTo v-if="showResend" @closeResend="closeResend" :showSrManagerInfo="showSrManagerInfo"/>
  </div>
</template>
<script>
  import ApplyAgree from './ApplyAgree'
  import ResendTo from './ResendTo'
  import DetailList from './DetailList'
  export default {
    data(){
      return{
        showDetail:true,
        managerButton:false,
        showButton:false,
        onlineNow:false,
        showApplyAgree:false,
        ApplyAgreeTitle:'',
        showResend:false,
        showDetail1:true,
        type:'',
        imgUrl:'',
        resendButton:true,
      }
    },
    props:{
      showSrManagerInfo:Object,
      MPAttachments:Object,
      AssistMPList:Array,
    },
    components:{
      ApplyAgree:ApplyAgree,
      ResendTo:ResendTo,
      DetailList:DetailList,
    },
    methods:{
      editDetail(){
        this.showDetail1 = false
      },
      saveEditDetail(){
        if(!this.showDetail1){
          var vm = this
          var CurUser = JSON.parse(localStorage.getItem("CurUser"));
          this.showSrManagerInfo.IsAdminUpdate = true
          var formData = new FormData()
          formData.append("MiniProgram", JSON.stringify(this.showSrManagerInfo))
          $.ajax({
            url: vm.User.baseUrl + "/api/miniprogram",
            type: 'PUT',
            data: formData,
            beforeSend: function (xhr) {
              xhr.setRequestHeader('Authorization', 'BasicAuth ' + CurUser.TICKET)
            },
            xhrFields: {
              withCredentials: true
            },
            contentType: false,
            processData: false,
            success: function (result) {
              console.log(result)
            },
            error: function (XMLHttpRequest, textStatus, errorThrown) {
              console.log("err")
            },
            cache: false,
            dataType: 'json'
          });

        }

        this.showDetail1 = true
        //将修改后的数据即现在的数据
        //this.showSrManagerInfo
      },
      goback1(){
        this.$emit('closeSrManager')
      },
      closeApply(){
        this.showApplyAgree = false
      },
      nextapply1(){
        this.type = 'agree'
        this.showApplyAgree = true
      },
      nextapply2(){
        this.type = 'reject'
        this.showApplyAgree = true
      },
      nextapply3(){
        this.showResend=true
      },
      closeResend(){
        this.showResend=false
      },
    },
    mounted(){

      if(this.showSrManagerInfo.CurrStateNo==11||this.showSrManagerInfo.CurrStateNo==21||this.showSrManagerInfo.CurrStateNo==31||this.showSrManagerInfo.CurrStateNo==41){
        this.managerButton = true
      }
      if(this.showSrManagerInfo.CurrStateNo==11){
        this.ApplyAgreeTitle = '立项申请'
      }
      if(this.showSrManagerInfo.CurrStateNo==21){
        this.ApplyAgreeTitle = '商户号申请'
      }
      if(this.showSrManagerInfo.CurrStateNo==31){
        this.ApplyAgreeTitle = '方案审核'
      }
      if(this.showSrManagerInfo.CurrStateNo==41){
        this.ApplyAgreeTitle = '测试验收'
      }
      if(this.showSrManagerInfo.CurrStateNo==52){
        this.onlineNow = true
      }
      if(this.showSrManagerInfo.CurrStateNo==51){
        this.ApplyAgreeTitle = '上线确认'
        this.managerButton = true
      }
      this.imgUrl = ''
      for (var key in this.MPAttachments) {
        if (this.showSrManagerInfo.Id == key) {
          this.imgUrl = this.MPAttachments[key].icon
        }
      }
      for(var i = 0;i<this.AssistMPList.length;i++){
        if(this.AssistMPList[i].Id==this.showSrManagerInfo.Id){
            this.resendButton = false
        }
      }

    }


  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

  .middle_list {
    top: 0;
    right: 25px;
    position: absolute;
    height: 820px;
    width: 1130px;
    border: 1px solid rgba(187, 187, 187, 1);
    background-color: #fff;
    overflow: hidden;
  }

  .icon-fanhui1{
    font-size: 34px;
    position: absolute;
    right: 50px;
    line-height: 80px;
    color: #101010;
  }
  .mhead {
    background-color: #f8f8f8;
    height: 60px;
    text-align: left;
    border-bottom: 1px solid rgba(187, 187, 187, 1);
  }

  .mhead span {
    margin-left: 45px;
    line-height: 60px;
    font-size: 20px;
    color: #101010;
  }
  .wrap{
    overflow-y: scroll;
    overflow-x: hidden;
    width: 100%;
    height: 770px;
    padding-bottom: 30px;
  }
.head_detail{
  width: 90%;
  height: 200px;
  text-align: left;
  color: #101010;
  font-family: SourceHanSansSC-regular;
  position: relative;
  margin: 0 auto;
  margin-top: 50px;
}
.action{
  position: relative;
  left: 370px;
  margin: 20px 0;
  text-align: left;
}
  /*.action .el-button{*/
    /*position: relative;*/
    /*right: 115px;*/
/*}*/
  .detail_logo{
    float: left;
    height: 100%;
    width: 233px;
    overflow: hidden;
    background-color: white;
    border-radius: 4px;
  }
  .detail_logo img{
    width: 200px;
    height: 200px;
    margin: auto;
  }
.detail_des{
  margin-left: 100px;
  display: inline-block;
  height: 100%;
}
  .detail_des .name{
    width: 100%;
    height: 40px;
  }
  .detail_des .name span{
    line-height: 40px;
  }
  .detail_des .name span:first-child{
    display: inline-block;
    max-width: 300px;
    overflow: hidden;
    vertical-align: top;
    text-overflow:ellipsis;
    white-space: nowrap;
    font-size: 25px;
    color: rgba(16, 16, 16, 1);
    font-family: SourceHanSansSC-bold;
  }
  .name span:nth-child(2){
    font-size: 18px;
    color: rgba(16, 16, 16, 1);
    font-family: SourceHanSansSC-bold;
    margin-left: 80px;
  }
  .bankname{
    margin-top: 15px;
    width: 100%;
    height: 29px;
  }
  .bankname span{
    color: #2282EF;
    font-size: 18px;
  }
  .des{
    position: absolute;
    top: 80px;
    left: 333px;
    margin-top: 21px;
    width: 580px;
    max-height:145px;
    overflow-y: scroll;
    padding: 5px 0 10px 0;
  }
  .des::-webkit-scrollbar {
    display:none
  }
  .des p{
    font-size: 16px;
    line-height: 20px;
  }
  .detail_content{
    text-align: left;
    clear: both;
    width: 90%;
    margin: 40px auto;
  }
  .businessname{
    width: 170px;
    display: inline-block;
    margin: 10px 20px 25px 45px;
  }
  .businessname p:first-child{

    font-size: 22px;
    color: rgba(16, 16, 16, 1);
    font-family: SourceHanSansSC-bold;
  }
  .businessname p:nth-child(2){
    margin-top: 10px;
    font-size: 20px;
    color: rgba(16, 16, 16, 0.6);
    font-family: SourceHanSansSC-regular;
  }
  .detail_des .name .stateok{
    color: #259B24;
  }
  .detail_des .name .statenotok{
    color: red;
  }
  .el-button{
    margin: 0 15px;
  }
  .onlineNow1{
    margin-left: 80px;
    margin-right: 10px;
    cursor: pointer;
    color: blue;
  }
  .onlineNow3{
    cursor: pointer;
    color: blue;
    margin-right: 25px;
  }
  .onlineNow{
    cursor: pointer;
    color: red;

  }
</style>
<style>
  .el-table::before {
    left: 0;
    bottom: 0;
    width: 100%;
    height: 0;
  }
</style>
