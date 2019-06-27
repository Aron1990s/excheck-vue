<template>
  <div>

    <el-dialog title="模板校验信息" :visible.sync="dialogTableVisible" width="45%">
      <el-dialog width="30%" title="内层 Dialog" :visible.sync="innerVisible" append-to-body>

      </el-dialog>
      <el-table :data="gridData" max-height="400">
        <el-table-column property="column_number" label="列" width="120"></el-table-column>
        <el-table-column property="column_type" label="类型" width="120"></el-table-column>
        <el-table-column property="column_format" label="校验格式" width="175"></el-table-column>
        <el-table-column property="allow_blank" label="是否可空" width="120"></el-table-column>
        <el-table-column
          fixed="right"
          label="操作"
          width="100">
          <template >
            <el-button @click="showInnerDialog" type="text" size="small">编辑</el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-dialog>


    <div style="width: 100%" align="center">
      <el-table
        :data="tableData"
        border
        center
        style="width: 1031px">
        <el-table-column
          prop="template_id"
          label="模板id"
          width="390">
        </el-table-column>
        <el-table-column
          prop="template_name"
          label="模板名称"
          width="120">
        </el-table-column>
        <el-table-column
          prop="remark"
          label="备注"
          width="200">
        </el-table-column>
        <el-table-column
          prop="create_time"
          label="创建时间"
          width="220">
        </el-table-column>
        <el-table-column
          label="操作"
          width="100">
          <template slot-scope="scope">
            <el-button @click="openDialog(scope.row)" type="text" size="small">查看</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <br>
    <el-pagination
      background
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="currentPage"
      :page-sizes="[100, 200, 300, 400]"
      :page-size="currentPageSize"
      layout="total, jumper, prev, pager, next, sizes"
      :total="totalCount">
    </el-pagination>
  </div>
</template>

<script>
  import axios from 'axios'

  export default {
    name: 'HelloWorld',
    data() {
      return {
        msg: 'Welcome to Your Vue.js App',
        currentPage: 1,
        currentPageSize: 100,
        totalCount: 800,
        tableData: [],
        gridData: [],
        dialogTableVisible: false,
        innerVisible: false
      }
    },
    created() {
      const url = 'http://127.0.0.1:8002/exCheck/templateInfoCtl/listTemplateInfo';
      axios.get(url).then(response => {
        var tempList = response.data.templateInfos;
        var list = new Array();
        for (var i = 0; i < tempList.length; i++) {
          var sub = {
            id: tempList[i].id,
            template_id: tempList[i].template_id,
            template_name: tempList[i].template_name,
            remark: tempList[i].remark,
            create_time: tempList[i].create_time
          }
          list.push(sub);
        }
        this.tableData = list;
      }).catch(error => {
        alert('error')
      })
    },
    methods: {
      handleSizeChange(val) {
        console.log(`每页 ${val} 条`);
      },
      handleCurrentChange(val) {
        console.log(`当前页: ${val}`);
        alert(this.currentPageSize);
      },
      handleClick(row) {
        console.log(row);
      },
      openDialog(row){
        this.dialogTableVisible = true
        const url = 'http://127.0.0.1:8002/exCheck/templateInfoCtl/findTemplatePrinciple?template_id='+row.template_id;
        axios.get(url).then(response => {
          var tempList = response.data.templatePrinciples;
          var list = new Array();
          for (var i = 0; i < tempList.length; i++) {
            var sub = {
              column_number: tempList[i].column_number,
              column_number: tempList[i].column_number,
              column_type: tempList[i].column_type == '1' ? '时间' : tempList[i].column_type == '2' ? '字符串': '数值',
              column_format: tempList[i].column_format,
              allow_blank: tempList[i].allow_blank == '0' ? '否' : '是',
            }
            list.push(sub);
          }
          this.gridData = list;
        }).catch(error => {
          alert('error')
        })
      },
      showInnerDialog(){
        this.innerVisible = true;
      }
    }
  }
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
