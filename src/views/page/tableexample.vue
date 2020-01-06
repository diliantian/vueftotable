
<template>
  <div class="workanaly-fulllink-container app-container">
    <el-tabs v-model="activeName" @tab-click="handleClick">
      <el-tab-pane label="管理" name="first">
         <el-form :inline="true" :model="queryparam" class="demo-form-inline">
            <el-form-item label="日期">
              <el-date-picker
              v-model="queryparam.selectDateInfo"
               :picker-options="pickerOptions"
              type="daterange"
              range-separator="至"
              start-placeholder="开始日期"
              end-placeholder="结束日期">
            </el-date-picker>
            </el-form-item>
            <el-form-item label="地区">
        
                <el-select v-model="queryparam.Areacode" placeholder="请选择">
                  <el-option
                    v-for="item in options"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value">
                  </el-option>
                </el-select>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="queryHandle">查询</el-button>
            </el-form-item>
            <el-form-item>
              <el-button >导出</el-button>
            </el-form-item>
        </el-form>
      </el-tab-pane>
      <el-tab-pane label="考核" name="second">222222</el-tab-pane>
  </el-tabs>
    <ftoTable  stripe   border  @cell-click="handleitemChange" :ajaxConfig="ajaxConfig"   height="250" @sort-change="sorthandle" :default-sort="sortMap"  :columnAttributes="tableHeadData">
              <template v-slot:first>
                  <el-table-column
                      label="序号"
                      type="index"
                      width="50">
                  </el-table-column>
             </template>
     </ftoTable>
  </div>
</template>

<script>
import { getList } from '@/api/table'
import moment from 'moment'
export default {
  filters: {
    statusFilter(status) {
      const statusMap = {
        published: 'success',
        draft: 'gray',
        deleted: 'danger'
      }
      return statusMap[status]
    }
  },
  data() {
    return {
       pickerOptions: {
        disabledDate (time) {
          let curDate = new Date().getTime();
          let lastDate = 30 * 24 * 3600 * 1000;
          let duration = curDate - lastDate;
          let current = moment(moment().format('YYYY-MM-DD 23:59:59')).valueOf();
          return time.getTime() > current || time.getTime() < duration;
        },
      },
      list: null,
      listLoading: true,
      activeName: 'first',
      queryparam:{
          selectDateInfo:{},
          Areacode:''
      },
      tableHeadData:[
          {
              prop:"author",
              label:"作者",
              className:'allowclick',
              allowclick:true
          },
          {
              prop:"display_time",
              label:"时间",
              sortable:'custom',
              className:'allowclick',
              allowclick:true
          },
          {
              prop:"status",
              label:"状态",
              sortable:'custom',
          }
      ],
       options: [{
          value: '选项1',
          label: '黄金糕'
        }, {
          value: '选项2',
          label: '双皮奶'
        }, {
          value: '选项3',
          label: '蚵仔煎'
        }, {
          value: '选项4',
          label: '龙须面'
        }, {
          value: '选项5',
          label: '北京烤鸭'
        }],
       sortMap:{
                prop:"display_time",
                order:"descending"
      },
      ajaxConfig:{
         requestapi:getList,
         dataKey:'items' //object2json
      }
    }
  },
  created() {
    //       <!--slot="first"
    //this.fetchData()
  },
  methods: {
    fetchData() {
      this.listLoading = true
      getList().then(response => {
        this.list = response.data.items
        console.log("this.list",this.list);
        this.listLoading = false
      })
    },
    handleitemChange(row, column, cell, event){
      let {property}=column;
      let Headitem=this.tableHeadData.find((item=>item.prop==property));
       //处理下钻
      if(Headitem.allowclick==true)
      {
        console.log("allowclick","aaaaaaaaa");
      }


    },
    sorthandle(){
        console.log("aaaaaaaa")
    },
    handleClick(tab, event) {
      console.log(tab, event);
    },
    queryHandle(){
       console.log("queryHandle-----");
    }
  }
}
</script>
<style lang="scss">
.workanaly-fulllink-container{
  .breadcrumb_marbttom{
    margin-bottom:10px;
  }
}
 
</style>