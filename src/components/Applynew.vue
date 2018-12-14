<template>
  <div class="middle_list">
    <div class="mhead">
      <span>新小程序申请</span>
    </div>
    <el-form label-position="right" label-width="120px" :model="formLabelAlign">
      <el-form-item label="应用名称">
        <el-input autofocus="true" v-model="formLabelAlign.PApplyFullName"></el-input>
      </el-form-item>
      <el-form-item label="名称首字母">
        <el-input v-model="formLabelAlign.PApplyFirstName"></el-input>
      </el-form-item>
      <el-form-item label="商户名">
        <el-input v-model="formLabelAlign.PMerchantName"></el-input>
      </el-form-item>
      <el-form-item label="扩展分行">
        <el-input v-model="formLabelAlign.PBranchName"></el-input>
      </el-form-item>
      <el-form-item label="商户类别">
        <el-input v-model="formLabelAlign.PMerchantType"></el-input>
      </el-form-item>
      <el-form-item label="申请人">
        <el-input v-model="formLabelAlign.PUserName" value="formLabelAlign.PUserName" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="申请人分行">
        <el-input v-model="formLabelAlign.PUserBranch"></el-input>
      </el-form-item>
      <el-form-item label="申请人ID">
        <el-input v-model="formLabelAlign.PUserNo" value="formLabelAlign.PUserNo" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="补充描述">
        <el-input type="textarea" autosize v-model="formLabelAlign.PDesc"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" plain @click="submitapply">提交申请</el-button>
        <el-button class="cancelbutton" type="info" @click="closeadd">取消</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        formLabelAlign: {
          PApplyFullName: '',
          PApplyFirstName: '',
          PMerchantName: '',
          PBranchName: '',
          PMerchantType: '',
          PUserName: '',
          PUserBranch: '',
          PUserNo: '',
          PDesc: '',
          CurrStateNo:'',
          CurrStateName:'',
          IsAdminUpdate:''
        }
      }
    },
    mounted(){
      var CurUser = JSON.parse(localStorage.getItem("CurUser"));
      if(CurUser){
        this.formLabelAlign.PUserName = CurUser.NAME
        this.formLabelAlign.PUserNo = CurUser.USID
      }
    },
    methods: {
      closeadd() {
        this.$emit('closeapplyadd')
      },
      submitapply() {
        var fd = this.formLabelAlign
        if (fd.PApplyFullName.trim() && fd.PApplyFirstName.trim() && fd.PMerchantName && fd.PBranchName && fd.PMerchantType && fd.PUserName && fd.PUserBranch && fd.PUserNo) {
          console.log(this.formLabelAlign);
          this.formLabelAlign.CurrStateNo = 11
          this.formLabelAlign.IsAdminUpdate = false
          this.setup()
        } else {
          this.$confirm('请将信息填写完整', '提示', {
            type: 'warning',
            showCancelButton: false,
            center: true
          })
        }

      },
      setup() {
        var vm = this
        var CurUser = JSON.parse(localStorage.getItem("CurUser"));
        var miniProgram = vm.formLabelAlign
        console.log(miniProgram);
        var formData = new FormData()
        formData.append("MiniProgram", JSON.stringify(miniProgram))
        formData.append("CurUser", JSON.stringify(CurUser))
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
                  message: h('i', { style: 'color: teal'}, '申请成功')
                });
              vm.$emit('applyaddSuccessed', vm.formLabelAlign)
            }

          },
          error: function (XMLHttpRequest, textStatus, errorThrown) {
            console.log("err")
            var h = vm.$createElement;
            vm.$notify({
              title: '操作提醒',
              message: h('i', { style: 'color: teal'}, '申请失败,请查看网络状态稍后再试')
            });
          },
          cache: false,
          dataType: 'json'
        });
      },
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

  .middle_list {
    z-index: 6;
    top: 0;
    right: 25px;
    position: absolute;
    height: 820px;
    width: 1130px;
    border: 1px solid rgba(187, 187, 187, 1);
    background-color: #fff;
    overflow: auto;
  }

  .mhead {
    background-color: #E0E0E0;
    height: 80px;
    text-align: left;
    border-bottom: 1px solid rgba(187, 187, 187, 1);
    margin-bottom: 52px;
  }

  .mhead span {
    margin-left: 45px;
    line-height: 80px;
    font-size: 20px;
    color: #101010;
  }

  .el-input {
    width: 630px;
    height: 40px;
    margin-left: -150px;
  }

  .el-textarea {
    width: 630px;
    margin-left: -150px;
  }

  .el-select {
    margin-right: 562px;

  }

  .el-form--label-right {
    margin-left: 150px;
  }

  .el-button {
    width: 112px;
    position: relative;
    right: 270px;
  }

</style>
<style>
  .el-form-item__label {
    color: rgba(16, 16, 16, 1);
    font-size: 18px;
    font-family: SourceHanSansSC-regular;
  }

</style>
