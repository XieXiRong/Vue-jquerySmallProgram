
<template>
  <div class="middle_list">
    <div class="mhead">
      <span>小程序详情</span>
      <i class="iconfont icon-fanhui1" @click="goback"></i>
    </div>
    <div class="wrap">
      <div class="head_detail">
        <div class="detail_logo">
          <img :src="imgsrc?User.baseUrl+imgsrc.RealValue:'../../static/images/Image1.png'" alt="">
        </div>
        <div class="detail_des">
          <div class="name">
            <span>{{showoneData.PApplyFullName}}</span>
            <span class="state"
                  :class="{'stateok':showoneData.CurrStateNo%2==0,
             'statenotok':showoneData.CurrStateNo==13||showoneData.CurrStateNo==23||showoneData.CurrStateNo==33||showoneData.CurrStateNo==43||showoneData.CurrStateNo==53}">{{showoneData.CurrStateName}}</span>
          </div>
          <div class="bankname">
            <span>{{showoneData.PUserBranch}}</span>
            <span>{{showoneData.PMerchantType}}</span>
          </div>
          <div class="des">
            <p>{{showoneData.PDesc}}</p>
          </div>
        </div>
      </div>
      <div class="action">
        <el-row>
          <el-button type="primary" v-if="fromHelp" @click="helpApply">协助审核</el-button>
          <el-button type="primary" v-html="actionname" v-if="leftbutton&&!fromHelp" @click="nextapply()"></el-button>
        </el-row>
      </div>

      <DetailList :showSrManagerInfo="showoneData" :showDetail="showDetail" :MPAttachments="MPAttachments"/>

      <div class="wrap_progress" v-if="fromHelp">
        <div class="progerss">
          <div class="circle" ref="lixiang"><span>立项申请</span></div>
          <i class="iconfont icon-zhishi_you" ></i>
          <div class="circle" ref="business"><span>商户号申请</span></div>
          <i class="iconfont  icon-zhishi_you" ></i>
          <div class="circle" ref="fangan"><span>方案审核</span></div>
          <i class="iconfont  icon-zhishi_you" ></i>
          <div class="circle" ref="test"><span>测试验收</span></div>
          <i class="iconfont  icon-zhishi_you" ></i>
          <div class="circle" ref="online"><span>发布上线</span></div>
        </div>
        <div class="seletitem">
          <div class="record">
            <div v-for="(item,index) in helpList" :key="index">
              <span class="time">{{new Date(item.CreateDate).Format("yyyy-MM-dd hh:mm")}}</span>
              <span class="name">{{item.Name}}</span>
              <span class="help">{{item.Operation}}</span>
              <span>{{item.Message}}</span>
            </div>
          </div>
        </div>
      </div>

    </div>
  <ResendTo v-if="showHelp" :showHelp="showHelp" :showSrManagerInfo="showoneData" @closeHelpApply="closeHelpApply"/>
  </div>
</template>

<script>
  import ResendTo from './ResendTo'
  import DetailList from './DetailList'
  export default {
    data(){
      return{
        showDetail:true,
        leftbutton:false,
        actionname:'',
        showHelp:false,
        helpList:[],
      }
    },
    props:{
      showoneData:Object,
      showone:Boolean,
      fromHelp:Boolean,
      MPAttachments:Object,
      imgsrc:Object,
    },

    components:{
      ResendTo:ResendTo,
      DetailList:DetailList,
    },
    methods:{
      closeHelpApply(){
        this.showHelp = false
      },
      helpApply(){
        this.showHelp = true
      },
      goback(){
        this.$emit('closeone',this.showoneData)
      },
      nextapply(){
        if(this.showoneData.CurrStateNo==12){
          this.$emit('addnewbusiness',this.showoneData)
        }else if(this.showoneData.CurrStateNo==22){
          this.$emit('addApplyplan',this.showoneData)
        }else if(this.showoneData.CurrStateNo==32){
          this.$emit('showTestSubmit',this.showoneData)
        }else if(this.showoneData.CurrStateNo==42){
          this.$emit('openOnlineSubmit',this.showoneData)
        }

      }
    },
    mounted(){
      Date.prototype.Format = function (fmt) {
        var o = {
          "M+": this.getMonth() + 1, //月份
          "d+": this.getDate(), //日
          "h+": this.getHours(), //小时
          "m+": this.getMinutes(), //分
          "s+": this.getSeconds(), //秒
          "q+": Math.floor((this.getMonth() + 3) / 3), //季度
          "S": this.getMilliseconds() //毫秒
        };
        if (/(y+)/.test(fmt)) fmt = fmt.replace(RegExp.$1, (this.getFullYear() + "").substr(4 - RegExp.$1.length));
        for (var k in o)
          if (new RegExp("(" + k + ")").test(fmt)) fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
        return fmt;
      }
//当前状态No；状态：11为立项申请中，12为立项通过/13为立项不通过，
      // 21为商户号申请中，22为商户号已颁发/23为商户号被拒绝，31为方案审核中，
      // 32为方案通过/33为方案不通过，41为测试申请中，42为测试通过/43为测试不通过，51为上线申请中，
      // 52为已上线/53为上线被拒绝

      if(this.showoneData.CurrStateNo==12){
        this.leftbutton = true
        this.actionname = '申请商户号'
      }else if(this.showoneData.CurrStateNo==22){
        this.leftbutton = true
        this.actionname = '提交方案'
      }else if(this.showoneData.CurrStateNo==32){
        this.leftbutton = true
        this.actionname = '提交测试'
      }else if(this.showoneData.CurrStateNo==42){
        this.leftbutton = true
        this.actionname = '提交上线'
      }

      //协助审核进度条样式
      if(this.fromHelp){
        if(this.showoneData.CurrStateNo==11){
          this.$refs.lixiang.classList.add('nowState')
        }else if(this.showoneData.CurrStateNo==21){
          this.$refs.lixiang.classList.add('already')
          this.$refs.business.classList.add('nowState')
        }else if(this.showoneData.CurrStateNo==31){
          this.$refs.lixiang.classList.add('already')
          this.$refs.business.classList.add('already')
          this.$refs.fangan.classList.add('nowState')
        }else if(this.showoneData.CurrStateNo==41){
          this.$refs.lixiang.classList.add('already')
          this.$refs.business.classList.add('already')
          this.$refs.fangan.classList.add('already')
          this.$refs.test.classList.add('nowState')
        }else if(this.showoneData.CurrStateNo==51){
          this.$refs.lixiang.classList.add('already')
          this.$refs.business.classList.add('already')
          this.$refs.fangan.classList.add('already')
          this.$refs.test.classList.add('already')
          this.$refs.online.classList.add('nowState')
        }else if(this.showoneData.CurrStateNo==52){
          this.$refs.lixiang.classList.add('already')
          this.$refs.business.classList.add('already')
          this.$refs.fangan.classList.add('already')
          this.$refs.test.classList.add('already')
          this.$refs.online.classList.add('already')
        }
      }
      var CurUser = JSON.parse(localStorage.getItem("CurUser"));
      var vm =this
      //获取审批记录
      $.ajax({
        url: vm.User.baseUrl +"/api/ApprovalRecord",
        type: 'GEt',
        data:{
          MPID:vm.showoneData.Id,
          Type:'allRecord'
        },
        beforeSend: function (xhr) {
          xhr.setRequestHeader('Authorization', 'BasicAuth ' + CurUser.TICKET)
        },
        xhrFields: {
          withCredentials: true
        },
        success: function (result) {
          if(result.StateCode){
            vm.helpList = result.Data
          }
        },
        error: function (XMLHttpRequest, textStatus, errorThrown) {
          console.log("协助审批接口调用失败")
        },
        cache: false,
        dataType: 'json'
      });

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
  .wrap{
    overflow-y: scroll;
    overflow-x: hidden;
    width: 100%;
    height: 770px;
    padding-bottom: 30px;
  }
  .el-progress-circle{
    background-color: #009688;
  }
  .mhead span {
    margin-left: 45px;
    line-height: 60px;
    font-size: 20px;
    color: #101010;
  }
.head_detail{
  width: 90%;
  height: 233px;
  text-align: left;
  color: #101010;
  font-family: SourceHanSansSC-regular;
  position: relative;
  margin: 0 auto;
  margin-top: 50px;
}
.action{
  position: relative;
  left: 380px;
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
  }
  .detail_logo img{
    width: 200px;
    height: 200px;
    margin: auto;
    border-radius: 4px;
  }
.detail_des{
  margin-left: 100px;
  float: left;
  height: 100%;
  width: 610px;
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
    margin-left: 150px;
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
    padding: 5px 0 10px 0;
  }
  .des::-webkit-scrollbar {
    display:none
  }
  .des p{
    font-size: 16px;
    line-height: 20px;
  }

  .detail_des .name .stateok{
    color: #259B24;
  }
  .detail_des .name .statenotok{
    color: red;
  }
  .wrap_progress{
    width: 100%;
  }
  .progerss{
    width: 100%;
    padding-left: 100px;
    height: 120px;
  }
  .circle{

    color: rgba(16, 16, 16, 1);

    border: 1px solid rgba(187, 187, 187, 1);

    font-size: 16px;
    font-family: Roboto;
    text-align: center;
    width: 100px;
    height: 100px;
    border-radius: 100%;
    float: left;
    margin-right: 28px;
  }
  .icon-zhishi_you{
    float: left;
    line-height: 100px;
    font-size: 30px;
    color: rgba(16, 16, 16, 0.6);
    margin-right: 15px;
  }
  .circle span{
    line-height: 100px;
  }
  .progerss .already{
    color: rgba(255, 255, 255, 1);
    border: 1px solid rgba(187, 187, 187, 1);
    background-color: #009688;

  }
  .progerss .nowState{
    background-color: rgba(255, 255, 255, 1);
    color: rgba(16, 16, 16, 1);
    border: 3px solid rgba(0, 150, 136, 1);
  }
  .seletitem {
    height: 150px;
    width: 100%;
    text-align: left;
    padding-left: 100px;
    margin: 10px 0;
  }
  .seletitem span {
    margin-right: 35px;
  }
  .record{
    padding: 10px 30px 10px 0;
    width: 737px;
    height: 100px;
    overflow: auto;
  }
  .record div{
    margin-bottom: 5px;
  }
  .record span{
    margin: 0;
    font-size: 16px;
  }
  .record .time{
    margin-right: 15px;
  }
  .record .name{
    margin-right: 28px;
  }
  .record .help{
    margin-right: 5px;
  }
</style>
