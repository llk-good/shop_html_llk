<template>
    <div align="center">
      <h1>属性信息</h1>

      <!--条件查询-->
      <el-form :inline="true" :model="eachFrom" class="demo-form-inline">
        <el-form-item label="名称">
          <el-input  v-model="eachFrom.nameCH" placeholder="属性名称"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="eachFromSub">查询</el-button>
          <el-button type="success" @click="showAddFrom" >新增</el-button>
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
            :formatter="formatterPro"
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
      <el-dialog :title="Propname" :visible.sync="ShowValueTable" width="400">
        <el-button
          type="danger" icon="el-icon-circle-plus-outline"
          size="mini"
          @click="addValue"
        >新增</el-button>
        <el-table :data="gridData">
          <el-table-column property="name" label="属性" ></el-table-column>
          <el-table-column property="nameCH" label="中文" ></el-table-column>
          <el-table-column property="isDel" label="是否展示" :formatter="forValueAdd" ></el-table-column>


          <el-table-column
            prop="cid"
            label="操作">
            <!-- &lt;!&ndash; 自定义模板  &ndash;&gt;-->
            <template slot-scope="scope">
              <el-button
                type="warning" icon="el-icon-edit"
                size="mini"
                @click="toUpdate(scope.$index, scope.row)"
              >修改{{Propname}}</el-button>
              <el-button
                type="danger" icon="el-icon-circle-close"
                size="mini"
               >删除</el-button><!-- @click="ShowValue(scope.$index, scope.row)"-->

            </template>

          </el-table-column>



        </el-table>
      </el-dialog>

      <!--子表新增-->
      <el-dialog title="新增属性值"  :visible.sync="addValueHtml"  >
        <el-form ref="addproValue" :rules="rules" :model="addproValue" label-width="180px"  >


          <el-form-item label="属性值名称" prop="name">
            <el-input v-model="addproValue.name"></el-input>
          </el-form-item>

          <el-form-item label="属性值中文名" prop="nameCH">
            <el-input v-model="addproValue.nameCH"></el-input>
          </el-form-item>

          <!--<el-form-item label="pid" prop="nameCH">
            <el-input v-model="addproValue.propId"></el-input>
          </el-form-item>-->

          <el-form-item>
            <el-button type="primary" @click="addproValueSubmit('addproValue')">立即新增</el-button>

          </el-form-item>
        </el-form>
      </el-dialog>

      <!--子属性的修改-->
      <el-dialog title="修改属性值"  :visible.sync="updateValueHtml"  >
        <el-form ref="updateproValue" :rules="rules" :model="updateproValue" label-width="180px"  >

          <el-form-item label="属性值名称" prop="name">
            <el-input v-model="updateproValue.name"></el-input>
          </el-form-item>

          <el-form-item label="属性值中文名" prop="nameCH">
            <el-input v-model="updateproValue.nameCH"></el-input>
          </el-form-item>

          <el-form-item label="是否展示" prop="nameCH">
            <el-input v-model="updateproValue.propId"></el-input>
          </el-form-item>

          <el-form-item>
            <el-button type="primary" @click="updateValueSubmit('updateproValue')">立即修改</el-button>

          </el-form-item>
        </el-form>
      </el-dialog>


      <!--新增模板-->
      <el-dialog title="商品属性信息录入" :visible.sync="dialogFormVisible">
        <el-form ref="form" :rules="rules" :model="form" label-width="80px">

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
          /*条件查询*/
          eachFrom:{
            nameCH:"",
          },
          /*  新增相关的数据  */
          dialogFormVisible:false,
          form:{
            typeId:-1
          },
          /*  需要数据做转换   */
          types:[],
          tyoeData:[],
          Propdata:[],

          /*分页*/
          page:1,
          size:2,
          count:0,
          sizes:[2,3,5,10],
          /*属性值维护*/
          ShowValueTable:false,
          gridData:[],
          /*子表新增*/
          Propname:"",
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
          rules: {
            name: [
              { required: true, message: '请输入属性名称', trigger: 'blur' },
              { min: 2, max: 15, message: '长度在 2 到 15 个字符', trigger: 'blur' }
            ],
            nameCH: [
              { required: true, message: '请输入属性中文名称', trigger: 'blur' },
              { min: 2, max: 15, message: '长度在 2 到 15 个字符', trigger: 'blur' }
            ],
            typeId: [
              { required: true, message: '请选择一个类型', trigger: 'change' }
            ]
          }
        }
      },created:function(){
        this.queryData();
        this.formaterTypeData();
      },
      methods:{
          /*条件查询*/
        eachFromSub:function () {
          this.queryData();
        },
      handleCurrentChange:function(page){ //根据页数跳转页面
        this.page=page;
        this.queryData();
      },handleSizeChange:function(size){ //跳转页面
        this.size=size;
        this.queryData();
      },
        /*展示*/
          queryData:function(){
            this.$ajax.get("http://localhost:8080/api/prop/getData?page="+this.page+"&size="+this.size).then(res=>{
              this.Propdata = res.data.data.list;
              this.count = res.data.data.count;
            })
          },
        /*新增*/

        /* 新增相关的  */
        //处理分类的下拉框   [{id:1,"name":"",pid:2},{}]
        // {"id":7,name:"分类/电子产品/手机"},
        formaterTypeData:function () {
          this.$ajax.get("http://localhost:8080/api/type/getData").then(res=>{
              this.tyoeData = res.data.data;
            //{"id":7,name:"分类/电子产品/手机"},
            //先找到子节点的数据   this.types;
            this.getChildrenType();

            //遍历所有的子节点
            for (let i = 0; i <this.types.length ; i++) {
              this.typeName="";//临时存储
              //处理子节点的name值
              this.chirdName(this.types[i]);
              //给name重新赋值
              this.types[i].name=this.typeName.split("/").reverse().join("/");
          }

          })
        },
        chirdName:function(node){
          if (node.pid != -1){
            this.typeName+="/"+node.name;
            //上级节点
            for (let i = 0; i < this.tyoeData.length; i++) {
              if (node.pid == this.tyoeData[i].id){
                this.chirdName(this.tyoeData[i]);
                break;
              }
            }
          }else {
            this.typeName+="/"+node.name;
          }
        },

        getChildrenType:function () {
          //遍历所有的节点数据
          //调用方法判断是否是子节点
          for (let i = 0; i <this.tyoeData.length ; i++) {
            var node = this.tyoeData[i];
            this.isChirdrenNode(node);
          }
        },
        isChirdrenNode:function (node) {
          let rs = true;
          for (let i = 0; i <this.tyoeData.length ; i++) {
            //父节点
            if (node.id==this.tyoeData[i].pid){
              rs = false;
              break;
            }
          }
          //子节点
          if (rs==true){
            this.types.push(node);
          }
        },

        showAddFrom:function () {
          this.dialogFormVisible = true;
        },
        add:function () {
          this.form.isSKU = this.isSKUb ? 0:1;
          this.$ajax.post("http://localhost:8080/api/prop/add",this.$qs.stringify(this.form)).then(res=>{
            this.dialogFormVisible = false;
          })
        },
        ShowValue:function (index , row) {

          this.Propname = row.name+"的属性值";
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


        },
        addproValueSubmit:function (addproValue) {
          debugger
          /*this.$refs[addproValue].validate((valid) => {
            if (valid) {
              this.$ajax.post("http://localhost:8080/api/propvalue/add", this.$qs.stringify(this.addproValue)).then(res => {
                this.addValueHtml = false;
                this.queryDataValue(this.addproValue.propId);
              }).catch(err => {
                console.log(err)
              })
            } else {
              console.log('error submit!!');
              return false;
            }
          })*/
          this.$ajax.post("http://localhost:8080/api/propvalue/add", this.$qs.stringify(this.addproValue)).then(res => {
            this.addValueHtml = false;
            this.queryDataValue(this.addproValue.propId);
          }).catch(err => {
            console.log(err)
          })
        },toUpdate:function (index,row) {
          this.updateValueHtml = true;
          this.updateproValue = row;
        },updateValueSubmit:function (updateproValue) {
          this.$ajax.post("http://localhost:8080/api/propvalue/update",this.$qs.stringify(this.updateproValue)).then(res=>{
            this.updateValueHtml = false;
            this.queryDataValue(this.updateproValue.propId);
          })
        },

        /*属性值的转换*/
        formatterPro:function (a,b,c,d) {
          return c==0?"下拉框": c==1?"单选框": c==2?"复选框": c==3?"输入框":"";
        },forValueAdd:function (a,b,c,d) {
          return c==0?"是":"否";
        }
      }
    }
</script>

<style scoped>

</style>
