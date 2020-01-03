<template>
    <div>
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">删除</el-button>
        <el-table :data="category">
        <el-table-column prop="id" label="编号"></el-table-column>
        <el-table-column prop="name" label="栏目名称"></el-table-column>
        <el-table-column prop="num" label="序号"></el-table-column>
        <el-table-column prop="parentId" label="父母栏"></el-table-column>
        <el-table-column label="操作">
            <template v-slot="slot">
                <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                <a href="" @click.prevent="toUpdateHandler">修改</a>
            </template>
        </el-table-column>
        </el-table>
        <!--分页开始-->
         <el-pagination
          layout="prev, pager, next"
           :total="50">
         </el-pagination>
        <!-- 模态框-->
        <el-dialog
        title="添加栏目信息"
        :visible.sync="visible"
       width="40%">
        --{{form}}
     <el-form :model="form" label-width="80px">
         <el-form-item label="栏目名称">
             <el-input v-model="form.name" ></el-input>
         </el-form-item>
         <el-form-item label="序号">
             <el-input  v-model="form.num" ></el-input>
         </el-form-item>
     </el-form>
      <span slot="footer" class="dialog-footer">
      <el-button @click="closeModalHandler" size="small">取 消</el-button>
      <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
      </span>
</el-dialog>

    </div>

</template>
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
             let url = "http://localhost:6677/category/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到customers中,this指向外部函数的this
            this.category = response.data;

        })
        },
        submitHandler(){
               //通过request与后台进行交互
               //this.form对象---字符串--->后台
        let url = "http://localhost:6677/category/saveOrUpdate"
        request({
            url,
            method:"POST",
            headers:{
                "Content-Type":"application/x-www-form-urlencoded"
            },
            data:querystring.stringify(this.form)

        }).then((response)=>{
            //模态框关闭
            this.closeModalHandler();
            //刷新
            this.loadData();
            //提示消息
            this.$message({
                type:"success",
                message:response.message
            })
        })



        },
        toDeleteHandler(){
            //确认
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
        })
        

        },
        closeModalHandler(){
             this.visible = false;
        },
        toUpdateHandler(){
            this.visible = true;
        },
        toAddHandler(){
            this.visible = true;
        }

    },
    //用于存放要向网页显示的数据
    data(){
       
        return{
            visible: false,
            category:[],
            form:{ type:"category"}

        }
    },
    created(){
        //vue实例完毕
       this.loadData();

    }
}
</script>
<style scoped>

</style>