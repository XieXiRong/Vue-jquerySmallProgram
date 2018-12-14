<template>
<div class="header">
  <div class="header1">
    <div class="headl">
      <span>小程序管理平台</span>
    </div>
    <div class="headr">
      <img src="../../static/images/wallhaven-665333.jpg" alt="">
      <span class="hname">{{name}}</span>
      <i class="iconfont icon-tuichu" @click="logout"></i>
      <span class="tuichu" @click="logout">退出</span>
    </div>
  </div>

</div>

</template>

<script>
  export default {
    data(){
      return{
        name:''
      }
    },
    mounted(){
      var CurUser = JSON.parse(localStorage.getItem("CurUser"));
      if(CurUser){
        this.name = CurUser.NAME
      }
    },
    methods:{
      logout(){
        localStorage.removeItem("CurUser")
        this.name = '请返回一事通登录'
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .header{
    height: 70px;
    background-color: white;
    display: flex;
    align-items: center;
    position: relative;
    width: 100%;
    margin-bottom: 25px;
  }
  .header1{
    height: 70px;
    background-color: white;
    display: flex;
    align-items: center;
    position: relative;
    width: 1440px;
    margin: 0 auto;
  }
  .headr{
    position: absolute;
    right: 28px;
    display: flex;
    align-items: center;
  }
  .hname{
    font-size: 16px;
    color: #101010;
  }
  .headl{
    margin-left: 40px;
    display: flex;
    align-items: center;
  }
  .headl span{
    font-size: 22px;
    color: black;
  }
  .hname{
    margin-right: 40px;
  }
  .headr img{
    width: 35px;
    height: 35px;
    border-radius: 50%;
    margin-right: 10px;
  }
  .icon-tuichu{
    margin-right: 10px;
    color: #E51C23;
    font-size: 30px;
    font-weight: bold;
    cursor: pointer;

  }
  .tuichu{
   cursor: pointer;
    font-size: 16px;
    color: #E51C23;
    font-family: SourceHanSansSC-bold;
  }
</style>
