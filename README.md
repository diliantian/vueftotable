```bash
vue element-ui el-table进行二次封装，采用非侵入式封装。element-ui升级之后仍然可用。
为什么要对进行二次封装？
1 element-ui组件的部分样式不满足当前项目的需求
2 产品要求排序方式只有asc，desc两个，不可取消排序。
3 提高代码复用性
```

```bash
# clone the project
git clone https://github.com/diliantian/vueftotable.git
#建议不要用cnpm安装 会有各种诡异的bug 可以通过如下操作解决 npm 下载速度慢的问题
npm install --registry=https://registry.npm.taobao.org


```
```bash
  #全局引用import ftoTable from '@/components/ftotable/index';
  Vue.component('ftoTable', ftoTable);

    <ftoTable
      stripe
      border
      @cell-click="handleitemChange"
      :ajaxConfig="ajaxConfig"
      :Action="Action"
      height="250"
      @sort-change="sorthandle"
      :default-sort="sortMap"
      :columns="tableHeadData"
    >
      <template v-slot:first>
        <el-table-column label="序号" type="index" width="50"></el-table-column>
      </template>
    </ftoTable>

    export default {
        data() {
            return {

                  tableHeadData: [
                        {
                        prop: "author",
                        label: "作者",
                        className: "allowclick",
                        allowclick: true //允许下钻
                        },
                        {
                        prop: "display_time",
                        label: "时间",
                        sortable: "custom",
                        className: "allowclick",
                        allowclick: true
                        },
                        {
                        prop: "status",
                        label: "状态",
                        sortable: "custom"
                        }
                    ],
                      ajaxConfig: {
                        requestapi: getList,# getList=>function 查询数据接口
                        dataKey: "items"
                    },
            }
        }

    }

    #columns 表头 必填项
    #ajaxConfig 选填
    #Action: "" // Action=>query 执行查询 选填
    #除了这3个参数，其他用法与el-table api完成一致,妈妈再也不用担心element-ui 属性和方法更新了！
```
