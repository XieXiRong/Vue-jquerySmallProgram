<template>
  <div class="middle_list">
    <div class="list_wrap">
      <div class="mhead">
        <span>{{ApplyAgreeTitle}}</span>
        <i class="iconfont icon-icon-" @click="closeApplyAgree"></i>
      </div>
      <div v-loading="loading">
        <div class="seletitem">
          <span>审批意见</span>
          <el-input
            type="textarea"
            :rows="5"
            placeholder="请输入审批意见"
            v-model="OpContent">
          </el-input>
        </div>
        <div class="seletitem" v-if="ApplyAgreeTitle=='上线确认'">
          <span>检查项</span>
          <el-checkbox-group v-model="checkList">
            <el-checkbox label="RHasMerchantNo">商户号已颁发</el-checkbox>
            <br>
            <el-checkbox label="RHasDoc">方案文档已提交</el-checkbox>
            <br>
            <el-checkbox label="RHasTest">已测试验收</el-checkbox>
          </el-checkbox-group>
        </div>
        <el-row>
          <el-button type="primary" plain @click="submitNew">提交</el-button>
          <el-button type="info" @click="closeApplyAgree">取消</el-button>
        </el-row>
      </div>

    </div>


  </div>
</template>

<script>
  export default {
    data(){
      return{
        OpContent:'',
        loading:false,
        checkList:[]
      }
    },
    props:{
      ApplyAgreeTitle:String,
      showSrManagerInfo:Object,
      type:String
    },
    methods:{
      closeApplyAgree(){
        this.$emit('closeApply')
      },
      submitNew(){
        this.loading = true
        if(this.type=='agree'){
          if(this.showSrManagerInfo.CurrStateNo==11){
            this.submitMethod(12)
          }else if(this.showSrManagerInfo.CurrStateNo==21){
            this.submitMethod(22)
          }else if(this.showSrManagerInfo.CurrStateNo==31){
            this.submitMethod(32)
          }else if(this.showSrManagerInfo.CurrStateNo==41){
            this.submitMethod(42)
          }else if(this.showSrManagerInfo.CurrStateNo==51){
            this.submitEnd()
          }
        }
        if(this.type=='reject'){
          if(this.showSrManagerInfo.CurrStateNo==11){
            this.submitMethod(13)
          }else if(this.showSrManagerInfo.CurrStateNo==21){
            this.submitMethod(23)
          }else if(this.showSrManagerInfo.CurrStateNo==31){
            this.submitMethod(33)
          }else if(this.showSrManagerInfo.CurrStateNo==41){
            this.submitMethod(43)
          }else if(this.showSrManagerInfo.CurrStateNo==51){
            this.submitMethod(53)
          }
        }
      },
      submitMethod(num){
        var vm = this
        var CurUser = JSON.parse(localStorage.getItem("CurUser"));
        var formData = new FormData()
        this.showSrManagerInfo.CurrStateNo = num
        formData.append("MiniProgram", JSON.stringify(this.showSrManagerInfo))
        formData.append("OpContent", JSON.stringify(vm.OpContent))
        $.ajax({
          url: vm.User.baseUrl + "/api/miniprogram",
          type: 'Post',
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
            if(result.StateCode){
              vm.loading = false
              var h = vm.$createElement;
              vm.$notify({
                title: '操作提醒',
                message: h('i', { style: 'color: teal'}, '通过成功')
              });
              vm.closeApplyAgree()
            }
          },
          error: function (XMLHttpRequest, textStatus, errorThrown) {
            console.log("err")
            vm.loading = false
            var h = vm.$createElement;
            vm.$notify({
              title: '操作提醒',
              message: h('i', { style: 'color: teal'}, '操作失败')
            });
          },
          cache: false,
          dataType: 'json'
        });
      },
      submitEnd(){
        var vm = this
        var CurUser = JSON.parse(localStorage.getItem("CurUser"));
        if(this.checkList.indexOf('RHasMerchantNo')>=0){
          this.showSrManagerInfo.RHasMerchantNo =1
        }else{
          this.showSrManagerInfo.RHasMerchantNo =0
        }
        if(this.checkList.indexOf('RHasDoc')>=0){
          this.showSrManagerInfo.RHasDoc =1
        }else{
          this.showSrManagerInfo.RHasDoc =0
        }
        if(this.checkList.indexOf('RHasTest')>=0){
          this.showSrManagerInfo.RHasTest =1
        }else{
          this.showSrManagerInfo.RHasTest =0
        }
        var formData = new FormData()
        this.showSrManagerInfo.CurrStateNo = 52
        formData.append("MiniProgram", JSON.stringify(this.showSrManagerInfo))
        formData.append("OpContent", JSON.stringify(vm.OpContent))
        $.ajax({
          url: vm.User.baseUrl + "/api/miniprogram",
          type: 'Post',
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
            if(result.StateCode){
              var h = vm.$createElement;
              vm.$notify({
                title: '操作提醒',
                message: h('i', { style: 'color: teal'}, '通过成功')
              });
              vm.closeApplyAgree()
            }
          },
          error: function (XMLHttpRequest, textStatus, errorThrown) {
            console.log("err")
          },
          cache: false,
          dataType: 'json'
        });
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .middle_list {
    z-index: 6;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    position: absolute;
    border: 1px solid rgba(187, 187, 187, 1);
    background-color: rgba(16, 16, 16, 0.44);
  }
.el-checkbox-group{
  vertical-align: top;
  margin-left: 20px;
}
  .list_wrap{
    margin:100px auto;
    height: 640px;
    width: 927px;
    background-color: #fff;
    color: rgba(16, 16, 16, 1);
    font-size: 18px;
    font-family: SourceHanSansSC-regular;
  }
  .icon-icon-{
    position: absolute;
    right: 40px;
    line-height: 80px;
    font-size: 24px;
    font-weight: bold;

  }
  .el-textarea{
    width: 600px;
    vertical-align: top;
  }
  .seletitem {
    width: 100%;
    text-align: left;
    margin: 25px 0 25px 90px;
  }

  .seletitem span {
    margin-right: 35px;
  }

  .seletitem{
    width: 100%;
    text-align: left;
    margin: 35px 0 40px 55px;
  }
  .seletitem span{
    margin-right: 35px;
  }
  .mhead {
    background-color: #E0E0E0;
    height: 80px;
    text-align: left;
    border-bottom: 1px solid rgba(187, 187, 187, 1);
    margin-bottom: 22px;
    position: relative;
  }

  .mhead span {
    margin-left: 45px;
    line-height: 80px;
    font-size: 20px;
    color: #101010;
  }
  .el-input{
    width: 640px;
    height: 40px;
    margin-left: -75px;
  }
  .el-form--label-right{
    margin-left: 40px;
  }

  .el-button{
    margin: 20px  50px 0 50px;
  }
</style>
<style>
  .list_wrap .el-form-item__label{
    color: rgba(16, 16, 16, 1);
    font-size: 18px;
    font-family: SourceHanSansSC-regular;
  }
  .el-checkbox-group{
    display: inline-block;
  }
</style>
