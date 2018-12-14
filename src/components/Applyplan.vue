<template>
  <div class="middle_list">
    <div class="list_wrap">
      <div class="mhead">
        <span>提交方案</span>
        <i class="iconfont icon-icon-" @click="closeplan"></i>
      </div>
      <div v-loading="loading">
        <div class="pro_icon">
          <span>上传图标</span>
          <img v-if="imageUrl2" @mouseenter="enter" :src="imageUrl2" class="avatar">
          <i v-if="showicon" class="iconfont icon3 icon-icon-" @click="deleimg"></i>
          <i v-show="!imageUrl2" class="el-icon-picture avatar-uploader-icon"><input :class="imageUrl2?'abs':''" type="file" @change="selImage"></i>
        </div>
        <div class="seletitem">
          <span>默认topbar</span>
          <el-switch
            v-model="showOnMsite">
          </el-switch>
        </div>
        <div class="seletitem">
          <span>功能点说明</span>
          <el-input
            type="textarea"
            :rows="3"
            placeholder="请输入功能点说明"
            v-model="textarea">
          </el-input>
        </div>
        <div class="pro_icon">
          <span>附件</span>
          <i  class="el-icon-circle-plus-outline"><input type="file" @change="selefile"></i>
          <div class="listW">
            <div class="fileItem" v-for="(file,index) in fileList" :key="index">
              <span class="filel">{{file}}</span>
              <i class="iconfont icon3 icon-icon-" @click="delefile(index)"></i>
            </div>
          </div>
        </div>
        <el-row>
          <el-button type="primary" plain @click="submitload">提交</el-button>
          <el-button type="info"  @click="closeplan">取消</el-button>
        </el-row>
      </div>

    </div>

  </div>
</template>

<script>
  export default {
    data() {
      return {
        fileList: [],
        imageUrl: '',
        showOnMsite: true,
        textarea: '',
        imgName: '',
        showicon: false,
        fileName: '',
        fileUrlList: [],
        imgname: '',
        fileUrlList2: [],
        imageUrl2: "",
        loading:false,
      };
    },
    props: {
      apllyPlan: Object
    },
    methods: {
      deleimg() {
        this.imageUrl2 = ''
        this.showicon = false
      },
      enter() {
        this.showicon = true
      },
      selImage(e) {
        var vm = this
        var file = e.target.files[0];
        var type = file.type.split('/')[0];
        if (type != 'image') {
          return;
        }
        this.imgname = file.name
        vm.imageUrl = file
        var reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onloadend = function (e) {
          vm.imageUrl2 = reader.result;
        }
      },
      selefile(e) {
        var vm = this
        var file = e.target.files[0];
        this.fileList = this.fileList.concat(file.name)
        this.fileName = file.name
        vm.fileUrlList = vm.fileUrlList.concat(file)
      },
      delefile(index) {
        this.fileUrlList.splice(index, 1)
        this.fileList.splice(index, 1)
      },
      submitload() {
        if (!this.imageUrl) {
          this.$alert('请上传图片', '提示', {
            confirmButtonText: '确定',
            callback: action => {
              this.$message({
                type: 'info',
                message: `action: ${ action }`
              });
            }
          });
        } else {
          this.loading = true
          //此处还有一个showOnMsite:true,  向后台post的数据
          this.submitImg()
          this.submitPlan()
          this.submitFile()
        }

      },
      submitImg() {
        var vm = this
        var CurUser = JSON.parse(localStorage.getItem("CurUser"));
        var formData = new FormData()
        formData.append("MPID", this.apllyPlan.Id)
        formData.append("FieldID", this.apllyPlan.SIconId)
        formData.append("fieldName", 'SIconId')
        formData.append("file", this.imageUrl)
        $.ajax({
          url: vm.User.baseUrl + "/api/files",
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
            if(!result.StateCode){
              vm.loading = false
              var h = vm.$createElement;
              vm.$notify({
                title: '操作提醒',
                message: h('i', { style: 'color: teal'}, result.Message)
              });
            }
            if (result.StateCode) {
            }
          },
          error: function (XMLHttpRequest, textStatus, errorThrown) {
            console.log("err")
          },
          cache: false,
          dataType: 'json'
        });

      },
      submitFile() {
        var vm = this
        var CurUser = JSON.parse(localStorage.getItem("CurUser"));
        var formData = new FormData()
        formData.append("MPID", this.apllyPlan.Id)
        formData.append("FieldID", this.apllyPlan.SAttachId)
        formData.append("fieldName", 'SAttachId')
        this.fileUrlList.forEach(function (item,index) {
          formData.append("file1"+index, item)
        })
        $.ajax({
          url: vm.User.baseUrl + "/api/files",
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
            if(!result.StateCode){
              vm.loading = false
              var h = vm.$createElement;
              vm.$notify({
                title: '操作提醒',
                message: h('i', { style: 'color: teal'}, result.Message)
              });
            }
            if (result.StateCode) {
              vm.loading = false
              var h = vm.$createElement;
              vm.$notify({
                title: '操作提醒',
                message: h('i', { style: 'color: teal'}, '提交成功')
              });
              vm.closeplan()
            }
          },
          error: function (XMLHttpRequest, textStatus, errorThrown) {
            console.log("err")
          },
          cache: false,
          dataType: 'json'
        });

      },
      submitPlan() {
        if (this.showOnMsite) {
          this.apllyPlan.STopbar = 1
        } else {
          this.apllyPlan.STopbar = 0
        }
        this.apllyPlan.SDesc = this.textarea
        this.apllyPlan.CurrStateNo = 31
        var vm = this
        var CurUser = JSON.parse(localStorage.getItem("CurUser"));
        var miniProgram = vm.apllyPlan
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
            console.log(result)
            if(!result.StateCode){
              vm.loading = false
              var h = vm.$createElement;
              vm.$notify({
                title: '操作提醒',
                message: h('i', { style: 'color: teal'}, result.Message)
              });
            }
            if(result.StateCode){

            }

          },
          error: function (XMLHttpRequest, textStatus, errorThrown) {
            console.log("err")
          },
          cache: false,
          dataType: 'json'
        });
      },
    closeplan() {
      this.$emit('closeApplyplan')
    },
  }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .upload-demo{
    text-align: left;
    margin: 25px 0 25px 90px;
  }
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .el-textarea{
    width: 400px;
    vertical-align: top;
  }

  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 100px;
    height: 100px;
    line-height: 100px;
    text-align: center;
  }
  .avatar {
    width: 100px;
    height: 100px;
    border-radius: 5px;
    vertical-align: middle;
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
  .pro_icon{
    text-align: left;
    margin-bottom: 20px;
    position: relative;
  }
  .pro_icon span{
    margin-left: 90px;
  }
  .pro_icon input{
    position: absolute;
    font-size: 100px;
    right: 0;
    top: 0;
    opacity: 0;
  }
  .pro_icon p{
    margin-top: 10px ;
    margin-left: 225px;
  }
  .el-icon-picture{
    text-align: left;
    font-size: 55px;
    margin-left: 50px;
    line-height: 55px;
  }
  .el-icon-circle-plus-outline{
    font-size: 35px;
    line-height: 35px;
    margin-left: 90px;
  }
  .icon-icon- {
    position: absolute;
    right: 40px;
    line-height: 80px;
    font-size: 24px;
    font-weight: bold;

  }
  .pro_icon .icon3{
    position: relative;
    left: 0;
    bottom: 60px;
    font-size: 10px;
    margin-left: 10px;
  }
  .seletitem {
    width: 100%;
    text-align: left;
    margin: 25px 0 25px 90px;
  }

  .seletitem span {
    margin-right: 35px;
  }

  .mhead {
    background-color: #E0E0E0;
    height: 80px;
    text-align: left;
    border-bottom: 1px solid rgba(187, 187, 187, 1);
    margin-bottom: 32px;
    position: relative;
  }

  .mhead span {
    margin-left: 45px;
    line-height: 80px;
    font-size: 20px;
    color: #101010;
  }

  .el-input {
    width: 640px;
    height: 40px;
    margin-left: -75px;
  }

  .el-form--label-right {
    margin-left: 40px;
  }

  .el-button {
    margin: 20px 50px 0 50px;
  }
  .list_wrap .abs{
   position: absolute;
    opacity: 0;
}
  .fileItem{
    height: 35px;
    display: inline-block;
  }
  .fileItem .filel{
    margin-left: 160px;
    font-size: 16px;
  }
  .fileItem i{
    position: absolute;
    top: 0;
  }
  .listW{
    width: 100%;
  }
</style>

<style>
   .list_wrap #u1 .el-upload-list {
    width: 92px;
    margin: 0;
    padding: 0;
    list-style: none;
  }
  .list_wrap .el-upload-list--picture .el-upload-list__item{
    border: none;
  }
</style>
