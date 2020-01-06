

<template>
    <div class="fto-table-container" v-loading="showLoading">
      <el-table ref="ftotableDatatable" :data="tableData" v-bind="$attrs" v-on="$listeners" @sort-change="Handlesort"  >
              <slot name="first" />
                    <el-column v-for="(item, index) in columnAttributes"
                                      :key="index"
                                      :item="item" />
              <slot />
      </el-table>
       <el-pagination
        v-if="ShowPagination"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page.sync="Pageinfo.pageNo"
        :page-size="Pageinfo.limit"
         layout="total, prev, pager, next, jumper"
        :total="Pageinfo.pagetotal">
     </el-pagination>
    </div>

    
</template>
<script>
   import ElColumn from './el-column';
   var lang= require('lodash/lang');
   import { assign } from 'lodash/object';
   export default {
    name: "ftotableDatatable",
    components: {
        ElColumn
    },
    data() {
      return {
            Pageinfo: {
                pageNo: 1, //分页序号DESC
                limit: 10, //每页条数
                isPaging: true, //是否分页： true / false
                hasMore: false, //是否有下一页
                sortColumn: "",
                sortType: "",
                pagetotal:30
            },
            sortTypeMap:{
              ascending:'ASC',
              descending:'DESC',
              ASC:'ascending',
              DESC:'descending',
              ASCReverse:'descending',
              DESCReverse:'ascending'
            },
            fetchConfig:{
               pageIndexKey:'pageNo',
               pageSizeKey:'limit',
               dataKey:'tbody',
               totalKey:"total"
            },
            ShowPaginatFlag:true,
            showLoading:false,
            tableData:[]
            
      }
    },
    computed: {
      dataKey(){
        let DataKeyStr='';
        if(!lang.isEmpty(this.ajaxConfig.dataKey))
        {
           DataKeyStr=this.ajaxConfig.dataKey;
        }
        else{
           DataKeyStr=this.fetchConfig.dataKey;
        }
        return DataKeyStr;
      }
    },
    props: {
          serverParams: { // 查询参数
            type: Object
          },
          columnAttributes: { // 指定表格属性
                type: Array,
                default: () => []
          },
          pageParams:{
             type: Object,
             default: () => {}
          },
          ajaxConfig:{
              type: Object,
              default: () => {
              }
          },
          ShowPagination:{
              type: Boolean,
              default: () => true
          }
    },
    methods: {
        Handlesort(resdata){
          let {column, prop, order}=resdata;
           let ftotableDatatable=this.$refs.ftotableDatatable;
           if(column===null)
           {
               let orderType=this.sortTypeMap[this.Pageinfo.sortType+'Reverse'];
               let sortColumn=this.Pageinfo.sortColumn;
               ftotableDatatable.sort(sortColumn,orderType);
           }
           else
           {
                   this.Pageinfo.sortColumn=prop;
                   this.Pageinfo.sortType=this.sortTypeMap[order];
           }
           this.fetchHandle();
     
        },
        fetchHandle(){
           this.$set(this.Pageinfo,'pageNo',1);
           this.Pageinfo.pageNo=1;
           this.FetchAjaxData();
        },
        FetchAjaxData(){
            let ajaxparam = {
                "sortColumn": this.Pageinfo.sortColumn,
                "sortType": this.Pageinfo.sortType,
                "paging": true
            };
           if(lang.isEmpty(this.ajaxConfig.pageIndexKey))
           {
                ajaxparam[this.fetchConfig.pageIndexKey] = this.Pageinfo.pageNo;
                ajaxparam[this.fetchConfig.pageSizeKey] = this.Pageinfo.limit;
           }
            let  queryparam = assign({},  this.serverParams, ajaxparam);
            if (!this.ajaxConfig.requestapi) {
              throw new Error('requestapi is not a function!');
            } else {
              this.showLoading = true;
              this.ajaxConfig.requestapi(ajaxparam).then((res) => {
                 this.showLoading = false;
                let _res=res['data'];
               let ResList = res['data'] && _res[this.dataKey];
               let { thead, tbody } = ResList;
               if(Array.isArray(tbody) && tbody.length>0)
               {
                    this.tableData = tbody;
               }
               else
               {
                    this.tableData = _res[this.dataKey] || [];
               }
                console.log("res",this.dataKey);
                let total = _res.page ? _res.page.totalPage : _res.totalPage;
                this.loadingCount = 0;
              }).catch((error) => {

              });
      }

        },
        handleSizeChange(){
            this.fetchHandle();
        },
        handleCurrentChange(){
         
        }
    },
    created() {
        
    },
    mounted() {
       let ftotableDatatable=this.$refs.ftotableDatatable;
       let {prop,order}=ftotableDatatable.$props.defaultSort;
       console.log("ftotableDatatable",{
         prop,
         order
       });
       if(!lang.isEmpty(this.pageParams))
       {
         let Pageinfo=this.Pageinfo;
         let pageParams=this.pageParams;
         this.Pageinfo={
           ...Pageinfo,
           ...pageParams
         }
       }
       if(prop!=='')
       {
          this.Pageinfo.sortColumn=prop;
       }
       if(order!=='')
       {
         this.Pageinfo.sortType=this.sortTypeMap[order];
       }
       this.FetchAjaxData();
      
    },

   }
</script>
<style lang="scss">
   .fto-table-container{
     .el-table{
       margin-bottom:20px;
     }
   }
</style>