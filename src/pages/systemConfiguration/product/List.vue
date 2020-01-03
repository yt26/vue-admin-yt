<template>
  <div>
    <!-- 按钮 -->
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button> 
    <el-button type="danger" size="small">批量删除</el-button>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="products">
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="name" label="产品名称"></el-table-column>
      <el-table-column prop="price" label="价格"></el-table-column>
      <el-table-column prop="description" label="描述"></el-table-column>
      <el-table-column prop="status" label="所属产品"></el-table-column>
      <el-table-column label="操作">
        <template v-slot="slot">
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
          <a href="" @click.prevent="toUpdateHandler">修改</a>
        </template>
      </el-table-column>
    </el-table>
    <!-- /表格结束 -->
    <!-- 分页开始 -->
    <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
    <!-- /分页结束 -->
    <!-- 模态框 -->
    <el-dialog
      title="录入产品信息"
      :visible.sync="visible"
      width="60%">
        <!-- ---{{form}} -->
      <el-form :model="form" label-width="80px">
        <el-form-item label="名称">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="价格">
          <el-input  v-model="form.price"></el-input>
        </el-form-item>
        <el-form-item label="所属栏目">
          <el-input v-model="form.status"></el-input>
        </el-form-item>
        <el-form-item label="介绍">
          <el-input v-model="form.description"></el-input>
        </el-form-item>
         <el-form-item label="产品主图">
          <el-upload
            class="upload-demo"
            action="https://jsonplaceholder.typicode.com/posts/"
            :on-preview="handlePreview"
            :on-remove="handleRemove"
            :before-remove="beforeRemove"
            multiple
            :limit="3"
            :on-exceed="handleExceed"
            :file-list="fileList">
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
            </el-upload>
        </el-form-item>
      </el-form>

      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>
    <!-- /模态框 -->

  </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
  // 用于存放网页中需要调用的方法
  methods:{
      handleRemove(file, fileList) {
        console.log(file, fileList);
      },
      handlePreview(file) {
        console.log(file);
      },
      handleExceed(files, fileList) {
        this.$message.warning(`当前限制选择 3 个文件，本次选择了 ${files.length} 个文件，共选择了 ${files.length + fileList.length} 个文件`);
      },
      beforeRemove(file, fileList) {
        return this.$confirm(`确定移除 ${ file.name }？`);
      },
    loadData(){
      let url = "http://localhost:6677/product/findAll"
      request.get(url).then((response)=>{
        this.products = response.data;
      })
    },
    submitHandler(){
     
      // 通过request与后台进行交互，并且要携带参数
      let url = "http://localhost:6677/product/saveOrUpdate";
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:querystring.stringify(this.form)
      }).then((response)=>{
        // 模态框关闭
        this.closeModalHandler();
        // 刷新
        this.loadData();
        // 提示消息
        this.$message({
          type:"success",
          message:response.message
        })
      })

    },
    toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // 提示消息
        this.$message({
          type: 'success',
          message: '删除成功!'
        });
      })
      
    },
    toUpdateHandler(){
      this.visible = true;
    },
    closeModalHandler(){
      this.visible = false;
    },
    toAddHandler(){
      this.visible = true;
    }
  },
  // 用于存放要向网页中显示的数据
  data(){
    return {
      visible:false,
      products:[],
      form:{
        type:"product"
      }
    };
  },
  created(){
    // this为当前vue实例对象
    // vue实例创建完毕 
    this.loadData()

  }
}
</script>

<style scoped>
 
</style>