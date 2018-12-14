<template>
  <div class="middle_list">
    <div class="list_wrap">
      <div class="mhead">
        <span>提交上线</span>
        <i class="iconfont icon-icon-" @click="closeSubmit"></i>
      </div>
      <div v-loading="loading">
        <div class="seletitem">
          <span>应用首页</span>
          <el-input size="small"
                    v-model="onlineSubmit.MApplyHome">
          </el-input>
        </div>
        <div class="seletitem">
          <span>展示在首页宫格</span>
          <el-switch
            v-model="onlineSubmit.showOnMsite">
          </el-switch>
        </div>
        <div class="seletitem">
          <span>可搜索</span>
          <el-switch
            v-model="onlineSubmit.searchAble">
          </el-switch>
        </div>

        <el-row>
          <el-button type="primary" plain @click="subOnline">提交</el-button>
          <el-button type="info" @click="closeSubmit">取消</el-button>
        </el-row>
      </div>

    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        onlineSubmit: {
          showOnMsite: true,
          searchAble: true,
          MApplyHome: '',
        },
        loading:false,
      }
    },
    props:{
      onlineData:Object
    },
    methods: {
      closeSubmit() {
        this.$emit('closeOnlineSubmit')
      },
      subOnline() {
        var vm = this
        this.loading = true
        var CurUser = JSON.parse(localStorage.getItem("CurUser"));
        if(this.onlineSubmit.showOnMsite){
          vm.onlineData.RIsShowHome = 1
        }else{
          vm.onlineData.RIsShowHome = 0
        }
        if(this.onlineSubmit.searchAble){
          vm.onlineData.RIsSearch = 1
        }else{
          vm.onlineData.RIsSearch = 0
        }
        vm.onlineData.RApplyHome = this.onlineSubmit.MApplyHome
        vm.onlineData.CurrStateNo = 51
        var miniProgram = vm.onlineData
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
            console.log(result)
            if(result.StateCode){
              vm.loading = false
              var h = vm.$createElement;
              vm.$notify({
                title: '操作提醒',
                message: h('i', { style: 'color: teal'}, '申请成功')
              });
              vm.closeSubmit()
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
  .el-input{
    width: 500px;
    margin-left: 50px;
  }
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

  .list_wrap {
    margin: 100px auto;
    height: 640px;
    width: 927px;
    background-color: #fff;
    color: rgba(16, 16, 16, 1);
    font-size: 18px;
    font-family: SourceHanSansSC-regular;
  }

  .mhead {
    background-color: #E0E0E0;
    height: 60px;
    text-align: left;
    border-bottom: 1px solid rgba(187, 187, 187, 1);
    margin-bottom: 130px;
    position: relative;
  }

  .icon-icon- {
    position: absolute;
    right: 40px;
    line-height: 80px;
    font-size: 24px;
    font-weight: bold;
  }

  .mhead span {
    margin-left: 45px;
    line-height: 80px;
    font-size: 20px;
    color: #101010;
  }

  .seletitem {
    text-align: left;
    padding-left: 100px;
    margin-bottom: 50px;
  }

  .el-button {
    margin-right: 50px;
  }
</style>
<style>
  .el-form-item__label {
    color: rgba(16, 16, 16, 1);
    font-size: 18px;
    font-family: SourceHanSansSC-regular;
  }

</style>
