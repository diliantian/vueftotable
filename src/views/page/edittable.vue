<template>
 <el-row>
            <el-col span="24">
                <el-table size="mini" :data="master_user.data" border style="width: 100%" highlight-current-row>
                    <el-table-column type="index"></el-table-column>
                    <el-table-column v-for="(v,i) in master_user.columns" :prop="v.prop" :label="v.label" :width="v.width">
                        <template slot-scope="scope">
                            <span v-if="scope.row.isSet">
                                <el-input size="mini" placeholder="请输入内容" v-model="master_user.sel[v.prop]">
                                </el-input>
                            </span>
                            <span v-else>{{scope.row[v.prop]}}</span>
                        </template>
                    </el-table-column>
                    <el-table-column label="操作" width="100">
                        <template slot-scope="scope">
                            <span class="el-tag el-tag--info el-tag--mini" style="cursor: pointer;" @click="pwdChange(scope.row,scope.$index,true)">
                                {{scope.row.isSet?'保存':"编辑"}}
                            </span>
                            <!--<span v-else class="el-tag  el-tag--mini" style="cursor: pointer;" @click="pwdChange(scope.row,scope.$index,false)">
                                取消
                            </span>-->
                        </template>
                    </el-table-column>
                </el-table>
            </el-col>
            <el-col span="24">
                <div class="el-table-add-row" style="width: 99.2%;" @click="addMasterUser()"><span>+ 添加</span></div>
            </el-col>
        </el-row>
</template>
<script>
var generateId = {
        _count: 1,
        get(){return ((+new Date()) + "_" + (this._count++))}
};
 export default {
   data(){
       return {
              master_user: {
                    sel: null,//选中行   
                    columns: [
                        { prop: "type", label: "远程类型", width: 120 },
                        { prop: "addport", label: "连接地址", width: 150 },
                        { prop: "user", label: "登录用户", width: 120 },
                        { prop: "pwd", label: "登录密码", width: 220 },
                        { prop: "info", label: "其他信息" }
                    ],
                    data: [
                        {
                            type:"admin",
                            addport:"江西",
                            user:"admin",
                            pwd:'111',
                            info:"222"
                        }
                    ],
                },
       }
   },
   methods:{
                    //读取表格数据
                readMasterUser() {
                    //根据实际情况，自己改下啊 
                    this.master_user.data.map(i => {
                        i.id = generateId.get();//模拟后台插入成功后有了id
                        return i;
                    });
                },
                //添加账号
                addMasterUser() {
                    for (let i of this.master_user.data) {
                        if (i.isSet) return this.$message.warning("请先保存当前编辑项");
                    }
                    let j = { id: 0, "type": "", "addport": "", "user": "", "pwd": "", "info": "", "isSet": true, "_temporary": true };
                    this.master_user.data.push(j);
                    this.master_user.sel = JSON.parse(JSON.stringify(j));
                },
                //修改
                pwdChange(row, index, cg) {
                    //点击修改 判断是否已经保存所有操作
                    for (let i of this.master_user.data) {
                        if (i.isSet && i.id != row.id) {
                            this.$message.warning("请先保存当前编辑项");
                            return false;
                        }
                    }
                    //提交数据
                    if (row.isSet) {
                        //项目是模拟请求操作  自己修改下
                        let self=this;
                        let data = JSON.parse(JSON.stringify(self.master_user.sel));
                        for (let k in data) row[k] = data[k];
                        self.$message({
                            type: 'success',
                            message: "保存成功!"
                        });
                        //然后这边重新读取表格数据
                        self.readMasterUser();
                        row.isSet = false;
                    } else {
                        this.master_user.sel = JSON.parse(JSON.stringify(row));
                        row.isSet = true;
                    }
                }
   }
 }
</script>
<style >
.el-table-add-row {
    margin-top: 10px;
    width: 100%;
    height: 34px;
    border: 1px dashed #c1c1cd;
    border-radius: 3px;
    cursor: pointer;
    justify-content: center;
    display: flex;
    line-height: 34px;
}

</style>