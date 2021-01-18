<template>
    <div align="center">
      <h1>属性信息</h1>

      <!--条件查询-->
      <el-form :inline="true"  class="demo-form-inline">
        <el-form-item label="名称">
          <el-input  placeholder="属性名称"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary">查询</el-button>
          <el-button type="success">取消</el-button><!--@click="showAddFrom"-->
        </el-form-item>
      </el-form>
      <!--查询列表-->
      <div id="barndTable" align="center">
        <el-table
          :data="Propdata"
          width="500px"
        >

          <el-table-column
            prop="id"
            label="序号"
            width="60">
          </el-table-column>

          <el-table-column
            prop="typeName"
            label="分类"
            width="60">
          </el-table-column>

          <el-table-column
            prop="name"
            label="属性"
            width="100">
          </el-table-column>


          <el-table-column
            prop="type"
            label="属性类型"
            width="50px"
          >
          </el-table-column>

          <el-table-column
            prop="cid"
            label="操作">
           <!-- &lt;!&ndash; 自定义模板  &ndash;&gt;-->
            <template slot-scope="scope">
              <el-button
                type="warning" icon="el-icon-edit"
                size="mini"
                >修改</el-button><!--@click="toUpdate(scope.$index, scope.row)"-->


              <el-button type="warning"> 编辑</el-button>

              <el-button
                type="info" icon="el-icon-eleme"
                size="mini"
                @click="ShowValue(scope.$index, scope.row)">属性值维护</el-button>

            </template>

          </el-table-column>
        </el-table>
        <el-pagination
          @current-change="handleCurrentChange"
          @size-change="handleSizeChange"
          :page-sizes="sizes"
          :page-size="size"
          layout="total, sizes, prev, pager, next, jumper"
          :total="count"
        >
        </el-pagination>
      </div>


      <!--  属性值的维护数据  -->
      <el-dialog title="属性值信息" :visible.sync="ShowValueTable" width="400">
        <el-button
          type="danger" icon="el-icon-edit"
          size="mini"
          @click="addValue"
        >新增</el-button>
        <el-table :data="gridData">
          <el-table-column property="name" label="属性" width="150"></el-table-column>
          <el-table-column property="nameCH" label="中文" width="200"></el-table-column>


          <el-table-column
            prop="cid"
            label="操作">
            <!-- &lt;!&ndash; 自定义模板  &ndash;&gt;-->
            <template slot-scope="scope">
              <el-button
                type="warning" icon="el-icon-edit"
                size="mini"
                @click="toUpdate(scope.$index, scope.row)"
              >修改</el-button>
              <el-button
                type="danger" icon="el-icon-edit"
                size="mini"
               >删除</el-button><!-- @click="ShowValue(scope.$index, scope.row)"-->

            </template>

          </el-table-column>



        </el-table>
      </el-dialog>

      <!--子表新增-->
      <el-dialog title="新增属性值"  :visible.sync="addValueHtml"  >
        <el-form ref="addproValue"  :model="addproValue" label-width="180px"  >


          <el-form-item label="属性值名称" prop="name">
            <el-input v-model="addproValue.name"></el-input>
          </el-form-item>

          <el-form-item label="属性值中文名" prop="nameCH">
            <el-input v-model="addproValue.nameCH"></el-input>
          </el-form-item>

          <el-form-item label="pid" prop="nameCH">
            <el-input v-model="addproValue.propId"></el-input>
          </el-form-item>

          <el-form-item>
            <el-button type="primary" @click="addproValueSubmit('addproValue')">立即新增</el-button>

          </el-form-item>
        </el-form>
      </el-dialog>

      <!--子属性的修改-->
      <el-dialog title="修改属性值"  :visible.sync="updateValueHtml"  >
        <el-form ref="updateproValue" :model="updateproValue" label-width="180px"  >

          <el-form-item label="属性值名称" prop="name">
            <el-input v-model="updateproValue.name"></el-input>
          </el-form-item>

          <el-form-item label="属性值中文名" prop="nameCH">
            <el-input v-model="updateproValue.nameCH"></el-input>
          </el-form-item>

          <el-form-item>
            <el-button type="primary" @click="updateValueSubmit('updateproValue')">立即修改</el-button>

          </el-form-item>
        </el-form>
      </el-dialog>


      <!--新增模板-->
      <el-dialog title="商品属性信息录入" :visible.sync="dialogFormVisible">
        <el-form ref="form" :model="form" label-width="80px">

          <el-form-item label="分类">
            <el-select v-model="form.typeId" placeholder="请选择分类">
              <el-option label="请选择" :value="-1"></el-option>
              <el-option v-for="item in types" :key="item.id" :label="item.name" :value="item.id"></el-option>
            </el-select>
          </el-form-item>

          <el-form-item label="属性英文">
            <el-input v-model="form.name"></el-input>
          </el-form-item>
          <el-form-item label="属性中文">
            <el-input v-model="form.nameCH"></el-input>
          </el-form-item>

          <el-form-item label="属性类型">
            <el-radio-group v-model="form.type">
              <el-radio label="0">下拉框</el-radio>
              <el-radio label="1">单选框</el-radio>
              <el-radio label="2">复选框</el-radio>
              <el-radio label="3">输入框</el-radio>
            </el-radio-group>
          </el-form-item>

          <el-form-item label="是否SKU">
            <el-switch v-model="form.isSKUb"></el-switch>
          </el-form-item>
        </el-form>


        <div slot="footer" class="dialog-footer">
          <el-button >取 消</el-button>
          <el-button type="primary" @click="add" >确 定</el-button>
        </div>
      </el-dialog>
    </div>
</template>

<script>
    export default {
        name: "Prop",
      data(){
        return{
          /*  新增相关的数据  */
          dialogFormVisible:false,
          form:{
            typeId:-1
          },
          /*  需要数据做转换   */
          types:[
            {"id":7,name:"分类/电子产品/手机"},
            {"id":9,name:"分类/电子产品/笔记本"},
            {"id":8,name:"分类/服装/上衣"},
            {"id":10,name:"分类/服装/裤子"},
            {"id":11,name:"分类/家电/空调"},
            {"id":12,name:"分类/家电/洗衣机"},
            {"id":13,name:"分类/家电/冰箱"}],
          Propdata:[],
/*id:"",
            name:"",
            nameCH:"",
            typeId:"",
            type:"",
            isSKU:"",
            isDel:"",
            createDate:"",
            updateDate:"",
            author:"",*/

          /*分页*/
          page:1,
          size:2,
          count:0,
          sizes:[2,3,5,10],
          /*属性值维护*/
          ShowValueTable:false,
          gridData:[],
          /*子表新增*/
          addValueHtml:false,
          addproValue:{
            name:"",
            nameCH:"",
            propId:""
          },
          updateproValue:{
            name:"",
            nameCH:""
          },updateValueHtml:false,
        }
      },created:function(){
        this.queryData();
      },
      methods:{
      handleCurrentChange:function(page){ //根据页数跳转页面
        this.page=page;
        this.queryData();
      },handleSizeChange:function(size){ //跳转页面
        this.size=size;
        this.queryData();
      },
          queryData:function(){
            this.$ajax.get("http://localhost:8080/api/prop/getData?page="+this.page+"&size="+this.size).then(res=>{
              this.Propdata = res.data.data.list;
              this.count = res.data.data.count;
            })
          },
        showAddFrom:function () {
          this.dialogFormVisible = true;
        },add:function () {
          this.form.isSKU = this.isSKUb ? 0:1;
          this.$ajax.post("http://localhost:8080/api/prop/add",this.$qs.stringify(this.form)).then(res=>{
            this.dialogFormVisible = false;
          })
        },
        ShowValue:function (index , row) {
          this.addproValue.propId=row.id;
          this.updateproValue.propId=row.id;
          this.ShowValueTable = true;
          this.gridData.propId = row.id;
          this.queryDataValue(row.id);
        },
        queryDataValue:function (propId) {

          this.$ajax.get("http://localhost:8080/api/propvalue/getData?propid="+propId).then((res)=>{

            this.gridData=res.data.data;

          })
        },
        addValue:function () {
          this.addValueHtml = true;
          this.addproValue={}

        },
        addproValueSubmit:function (addproValue) {
          this.$ajax.post("http://localhost:8080/api/propvalue/add",this.$qs.stringify(this.addproValue)).then(res=>{
            this.addValueHtml = false;
            this.queryDataValue(this.addproValue.propId);
          })
        },toUpdate:function (index,row) {
          this.updateValueHtml = true;
          this.updateproValue = row;
        },updateValueSubmit:function (updateproValue) {
          this.$ajax.post("http://localhost:8080/api/propvalue/update",this.$qs.stringify(this.updateproValue)).then(res=>{
            this.updateValueHtml = false;
            this.queryDataValue(this.updateproValue.propId);
          })
        }

      }
    }
</script>

<style scoped>

</style>
