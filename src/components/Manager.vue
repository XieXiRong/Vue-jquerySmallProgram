<template>
  <div class="box">
    <span class="searchh">搜索</span>
    <el-input v-model="search" prefix-icon="el-icon-search" placeholder="请输入搜索内容" class="inputsearch"></el-input>
    <el-table
      show-overflow-tooltip="true"
      :data="tables"
      style="width: 100%">
      <el-table-column
        label="名称"
        align="center"
        width="180">
        <template slot-scope="scope">
          <span style="margin-left: 10px" v-html="format(scope.row.PApplyFullName)"></span>
        </template>
      </el-table-column>
      <el-table-column
        label="商户名"
        align="center"
        width="180">
        <template slot-scope="scope">
          <span v-html="format(scope.row.PMerchantName)"></span>
        </template>
      </el-table-column>
      <el-table-column
        label="拓展分行"
        align="center"
        width="180">
        <template slot-scope="scope">
          <span v-html="format(scope.row.PBranchName)"></span>
        </template>
      </el-table-column>
      <el-table-column
        label="申请人"
        align="center"
        width="130">
        <template slot-scope="scope">
          <span v-html="format(scope.row.PUserName)"></span>
        </template>
      </el-table-column>
      <el-table-column
        label="商户类型"
        align="center"
        width="130">
        <template slot-scope="scope">
          <span v-html="format(scope.row.PMerchantType)"></span>
        </template>
      </el-table-column>
      <el-table-column
        label="状态"
        align="center"
        width="130">
        <template slot-scope="scope">
          <span style="margin-left: 10px" class="statement"  v-html="format(scope.row.CurrStateName)"></span>
        </template>
      </el-table-column>
      <el-table-column label="操作" align="center">
        <template slot-scope="scope">
          <el-button
            size="mini"
            @click="handleEdit(scope.$index, scope.row)">查看</el-button>
        </template>
      </el-table-column>
    </el-table>
    <div class="searchs">
      <span>状态</span>
      <el-select v-model="value8" filterable placeholder="请选择">
        <el-option
          v-for="item in options"
          :key="item.value"
          :label="item.label"
          :value="item.value">
        </el-option>
      </el-select>
    </div>
  </div>
</template>

<script>
  import $ from 'jquery'
  export default {
    data () {
      return {
        value8:'',
        search: '',
        options: [
          {
            value: '',
            label: '全部数据'
          },{
          value: '立项申请中',
          label: '立项申请中'
        }, {
          value: '立项通过',
          label: '立项通过'
        }, {
          value: '立项不通过',
          label: '立项不通过'
        }, {
          value: '商户号申请中',
          label: '商户号申请中'
        }, {
          value: '商户号已颁发',
          label: '商户号已颁发'
        },{
          value: '商户号被拒绝',
          label: '商户号被拒绝'
        },
          {
            value: '方案审核中',
            label: '方案审核中'
          },
          {
            value: '方案通过',
            label: '方案通过'
          },
          {
            value: '方案不通过',
            label: '方案不通过'
          },
          {
            value: '测试申请中',
            label: '测试申请中'
          },
          {
            value: '测试通过',
            label: '测试通过'
          },
          {
            value: '测试不通过',
            label: '测试不通过'
          },
          {
            value: '已上线',
            label: '已上线'
          },
        ],
      }
    },
    props:{
      waited:Boolean,
      tableData:Array,
    },
    watch:{
      waited(){
        var statement = document.getElementsByClassName('statement')
        if(this.waited){
          for(var i=0;i<statement.length;i++){
            if(statement[i].classList.contains('stateok')){
              statement[i].classList.remove('stateok')
            }else if(statement[i].classList.contains('statenotok')){
              statement[i].classList.remove('statenotok')
            }
          }
        }
      }
    },
    updated(){
      var statement = document.getElementsByClassName('statement')
      setTimeout(function () {
        if(!this.waited){
          for(var i=0;i<statement.length;i++){
            if(statement[i].innerHTML=='立项通过'||statement[i].innerHTML=='商户号已颁发'||statement[i].innerHTML=='方案通过'||statement[i].innerHTML=='已上线'||statement[i].innerHTML=='测试通过'){
              statement[i].classList.add('stateok')
            }else if(statement[i].innerHTML=='立项不通过'||statement[i].innerHTML=='商户号被拒绝'||statement[i].innerHTML=='方案不通过'||statement[i].innerHTML=='测试不通过'){
              statement[i].classList.add('statenotok')
            }else if(statement[i].innerHTML=='测试商户号处理中'||statement[i].innerHTML=='正式商户号配置中'){
              statement[i].classList.add('stateing')
            }
          }
        }
      })

    },
    mounted(){
      var statement = document.getElementsByClassName('statement')
      setTimeout(function () {
        for(var i=0;i<statement.length;i++){
          if(statement[i].innerHTML=='立项通过'||statement[i].innerHTML=='商户号已颁发'||statement[i].innerHTML=='方案通过'||statement[i].innerHTML=='已上线'||statement[i].innerHTML=='测试通过'){
            statement[i].classList.add('stateok')
          }else if(statement[i].innerHTML=='立项不通过'||statement[i].innerHTML=='商户号被拒绝'||statement[i].innerHTML=='方案不通过'||statement[i].innerHTML=='测试不通过'){
            statement[i].classList.add('statenotok')
          }else if(statement[i].innerHTML=='测试商户号处理中'||statement[i].innerHTML=='正式商户号配置中'){
            statement[i].classList.add('stateing')
          }
        }
      })
      var j = 0
        for(let i=0;i<this.tableData.length;i++){
          if( this.tableData[i].CurrStateName == '立项申请中'||this.tableData[i].CurrStateName == '方案审核中'||this.tableData[i].CurrStateName == '测试申请中'||this.tableData[i].CurrStateName == '商户号申请中'||this.tableData[i].CurrStateName == '上线申请中'){
            j++
          }
        }
     this.$emit('waitCountNum',j)

    },
    computed: {
      tables () {
        var vm = this
        const search = this.search
        if (search) {
          console.log('this.tableData', this.tableData)
          return this.tableData.filter(dataNews => {
            return Object.keys(dataNews).some(key => {
              return String(dataNews[key]).toLowerCase().indexOf(search) > -1
            })
          })
        }else if(this.waited){  //待审批
          return this.tableData.filter(dataNews => {
            return dataNews.CurrStateName == '立项申请中'||dataNews.CurrStateName == '方案审核中'||dataNews.CurrStateName == '测试申请中'||dataNews.CurrStateName =='上线申请中'||dataNews.CurrStateName =='商户号申请中'
          })
        }else if(this.value8){
          return this.tableData.filter(dataNews => {
            return dataNews.CurrStateName == vm.value8
          })
        }
        return this.tableData
      },
    },

    methods: {
      handleEdit (index, row) {
        this.$emit('openSrManager',row)
      },
      format (val) {
        if (val.indexOf(this.search) !== -1 && this.search !== '') {
          return val.replace(this.search, '<font color="red">' + this.search + '</font>')
        } else {
          return val
        }
      }
    }

  }
</script>

<style scoped>
.box{

}
.searchh{
  position: absolute;
  left: 70px;
  top: 99px;
}
.stateok{
  color: #259B24;
}
  .inputsearch{
    width: 343px;
    height: 48px;
    display: flex;
    align-items: center;
    margin: 20px 0 30px 120px;
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
  top:85px;
}
.searchs span{
  position: relative;
  right: 20px;
  display: inline-block;
}
  .statenotok{
    color: red;
  }
.el-table::before {
  left: 0;
  bottom: 0;
  width: 100%;
  height: 0;
}
.stateing{
  color: blue;
}
</style>
<style>
  .el-table__body-wrapper{
    height: 580px;
    overflow-y: scroll;
  }
  #app .el-table thead {
    color: black;
    font-weight: 500;
  }
  .el-table::before {
    left: 0;
    bottom: 0;
    width: 100%;
    height: 0;
  }
</style>
