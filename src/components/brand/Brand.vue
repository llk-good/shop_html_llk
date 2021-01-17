<template>
  <div>
    <!--条件查询模板-->
 <h1>品牌新增</h1>
    <div id="searchDiv">
      <el-form :inline="true" :model="searchForm" class="demo-form-inline">

        <el-form-item label="名称">
          <el-input v-model="searchForm.name" placeholder="名称"></el-input>
        </el-form-item>
        <el-form-item label="首字母">
          <el-input v-model="searchForm.first" placeholder="首字母"></el-input>
        </el-form-item>


        <el-form-item>
          <el-button type="primary" @click="queryData(1,4)">查询</el-button>
          <el-button type="success" @click="addFormFlag=true">新增</el-button>
        </el-form-item>

      </el-form>
    </div>


      <!--查询列表-->
      <div id="barndTable">
        <el-table
          :data="BrandData"
          style="width: 100%">

          <el-table-column
            prop="id"
            label="序号"
            width="60">
          </el-table-column>

          <el-table-column
            prop="name"
            label="名称"
            width="100">
          </el-table-column>

          <el-table-column
            prop="first"
            label="首字母"
            width="50"
          >
          </el-table-column>

          <el-table-column
            prop="imgPath"
            label="图片">
            <!-- 按文本处理   :formatter="formatImg"    -->
            <!-- 模板处理  html  -->
            <template slot-scope="scope">
              <img width="50px" :src="'http://localhost:8080'+scope.row.imgPath"/>
            </template>

          </el-table-column>

          <el-table-column
            prop="brandDesc"
            label="品牌故事"
          width="150px">
          </el-table-column>

          <el-table-column
            prop="ord"
            label="排序"
            width="50px"
          >
          </el-table-column>
          <el-table-column
            prop="isDel"
            label="是否展示"
            width="50px"
          >
          </el-table-column>


          <el-table-column
            prop="author"
            label="修改人"
            width="80px"
          >
          </el-table-column>

          <el-table-column
            prop="createDate"
            label="创建时间">
          </el-table-column>
          <el-table-column
            prop="updateDate"
            label="修改时间">
          </el-table-column>

          <el-table-column
            prop="cid"
            label="操作">
            &lt;!&ndash; 自定义模板  &ndash;&gt;
            <template slot-scope="scope">
              <el-button
                type="primary" icon="el-icon-edit"
                size="mini"
                @click="toUpdate(scope.$index, scope.row)"></el-button>
              <!--<el-button
                size="mini"
                type="danger"
                @click="deleteCar(scope.$index, scope.row)">删除</el-button>-->
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

    <!--新增弹框-->
    <el-dialog title="录入新增品牌信息" :visible.sync="addFormFlag">
      <el-form :model="addForm" ref="addForm"   label-width="80px">

        <el-form-item label="名称" prop="name">
          <el-input v-model="addForm.name" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="首字母" prop="first">
          <el-input v-model="addForm.first" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="图片">
          <el-upload
            class="upload-demo"
            action="http://localhost:8080/api/brand/upload"
            :on-success="imgCallBack"
            list-type="picture">
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
          </el-upload>
        </el-form-item>

        <el-form-item label="品牌故事" prop="brandDesc">
          <el-input v-model="addForm.brandDesc" autocomplete="off":rows="3" type="textarea" placeholder="请输入内容" ></el-input>
        </el-form-item>

        <el-form-item label="排序" prop="ord">
          <el-input v-model="addForm.ord"></el-input>
        </el-form-item>


      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="addFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="addSubmit">新增提交</el-button>
      </div>
    </el-dialog>

    <!--修改弹框-->
    <el-dialog title="录入修改汽车信息" :visible.sync="updateFlag">

      <el-form :model="updateForm" ref="updateForm"   label-width="80px">

        <el-form-item label="名称" prop="name">
          <el-input v-model="updateForm.name" autocomplete="off" ></el-input>
        </el-form-item>
        <el-form-item label="首字母" prop="first">
          <el-input v-model="updateForm.first" autocomplete="off" ></el-input>
        </el-form-item>


        <el-form-item label="图片">
          <el-upload
            class="upload-demo"
            action="http://localhost:8080/api/brand/uploadFile"
            :on-success="imgCallBack"
            list-type="picture">
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
          </el-upload>
        </el-form-item>

        <el-form-item label="品牌故事" prop="brandDesc">
          <el-input v-model="updateForm.brandDesc"
                    autocomplete="off":rows="3"
                    type="textarea"
                    placeholder="请输入内容" ></el-input>
        </el-form-item>

        <el-form-item label="排序" prop="ord">
          <el-input v-model="updateForm.ord"></el-input>
        </el-form-item>

        <el-form-item label="是否显示" prop="isdel">
          <el-radio v-model="updateForm.isDel" :label="0" border>是</el-radio>
          <el-radio v-model="updateForm.isDel" :label="1" border>否</el-radio>
        </el-form-item>



      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="updateSubmit">立即修改</el-button>
        <el-button type="primary" @click="updateRest('updateForm')">取消</el-button>
      </div>
    </el-dialog>

  </div>

</template>

<script>
    export default {
        name: "Brand",
      data(){
        return{
          BrandData:[],
          /*搜索*/
          searchForm:{
            name:"",
            first:""
          },
          /*分页*/
          sizes:[2,3,5,10],
          count:0,
          page:1,
          size:2,
          /*新增*/
          addFormFlag:false,//新增弹框
          addForm:{
            name:"",
            first:"",
            imgPath:"",
            brandDesc:"",
            ord:"",
            isDel:"",
          },
          /*修改*/
          updateForm:{
            name:"",
            first:"",
            imgPath:"",
            brandDesc:"",
            ord:"",
            isDel:""
          },
          updateFlag:false

        }
      },
      /*方法*/
      methods:{
        queryData:function () {
          this.$ajax.get("http://localhost:8080/api/brand/getData?page="+this.page+"&size="+this.size
          ).then(res=>{
            console.log(res.data.data.list)
            this.BrandData = res.data.data.list;
            this.count = res.data.data.count;
          })
        }, handleCurrentChange:function(page){ //根据页数跳转页面
          this.page=page;
          this.queryData();
        },handleSizeChange:function(size){ //跳转页面
          this.size=size;
          this.queryData();
        },
        addSubmit:function () {
          this.$ajax.post("http://localhost:8080/api/brand/add",this.$qs.stringify(this.addForm)).then(res=>{
            this.addFormFlag = false;
            this.queryData();
          })
        },
        imgCallBack:function(response, file, fileList){//图片上传的回调函数
          this.addForm.imgPath=response.data;
        },

        toUpdate:function(index,row){
          this.updateFlag = true;
          this.imagpart = "";
          this.updateForm = row;
          this.imagpart = row.imagpart;

        },
        updateRest:function (updateFrom) {
          this.updateFlag = false;
          this.$refs[updateFrom].resetFields();
        },updateSubmit:function () {
          this.$ajax.post("http://localhost:8080/api/brand/update",this.$qs.stringify(this.updateForm)).then(res=>{
            this.updateFlag = false;
            location.reload();
          })
        }
        /* this.$ajax.post("http://192.168.1.224:8083/api/brand/update",this.$qs.stringify(this.updateForm)).then((res)=>{
              console.log(res);
              this.updateHtml=false;
              location.reload();
            }
          ).catch(err=>console.log(err))
       */
      },
      /*钩子函数*/
      created:function () {
        this.queryData();
      }
    }
</script>

<style scoped>

</style>
