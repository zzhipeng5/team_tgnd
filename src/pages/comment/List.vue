<template>
<!-- 顾客管理 -->
    <div>
        <!--按钮-->
        <el-button size="small" type="success" @click="toAddHandler">添加</el-button>
        <el-button size="small" type="danger">批量删除</el-button>
        <!--/按钮-->
        <!--表格-->
        <el-table :data="comments"> 
            <el-table-column  fixed="left" prop="id" label="编号"></el-table-column>
            <el-table-column  fixed="left" prop="content" label="内容"></el-table-column>
            <el-table-column  fixed="left" prop="commentTime" label="评论时间"></el-table-column>
            <el-table-column  fixed="left" prop="orderId" label="订单编号"></el-table-column>
            <el-table-column  fixed="right" label="操作">
                <template v-slot="slot">
                    <!--阻止默认跳转-->
                <el-button type="primary" icon="el-icon-delete" @click="toDeleteHandler(slot.row.id)"></el-button>
                <el-button type="primary" icon="el-icon-edit" @click="toUpdateHandler(slot.row)"></el-button>
                </template>
            </el-table-column>
        </el-table>
        <!--/表格-->
        <!--分页-->
        <!-- <el-pagination
            layout="prev, pager, next"
            :total="50">
        </el-pagination> -->
        <!--/分页-->
        <!--模态框-->
        <el-dialog
            title="录入顾客信息"
            :visible.sync="visible"
            width="60%">
             <el-form :model = "form" label-width="80px">
                
                <el-form-item label="内容">
                    <el-input v-model="form.content"/>
                </el-form-item>
                <el-form-item label="评论时间">
                    <el-input v-model="form.commentTime"/>
                </el-form-item>
                
            </el-form>  
            <span slot="footer" class="dialog-footer">
             <el-button size="small" @click="closeModelHandler">取 消</el-button>
             <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
                
            </span>
        </el-dialog>
        <!--/模态框-->
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放要向网页中存放的方法
    methods:{
        loadData(){
    let url = "http://localhost:6677/comment/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到customers中,this指向外部函数的this
            this.comments=response.data;
    })
        },
        
        submitHandler(){
            //this.form 对象---字符串-->后台
            //通过request与后台进行交互，并且要携带参数
           let url = "http://localhost:6677/comment/saveOrUpdate";
     
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //模态框关闭
                this.closeModelHandler();
                //提示消息
                this.loadData();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        toAddHandler(){
            //将form变为初始值
            this.form={
                type:"comment"
            }
            this.visible=true;
        },
        closeModelHandler(){
            this.visible=false;
        },
        toUpdateHandler(row){
            //模态框的表单中显示出当前行的信息
            this.form = row;
            this.visible = true;
        },
        toDeleteHandler(id) {
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
                //调用后台接口，完成删除操作
                let url = "http://localhost:6677/comment/deleteById?id="+id;
                request.get(url).then((response)=>{
                    //1.刷新数据
                    this.loadData();
                    //2.提示结果
                    this.$message({
                    type: 'success',
                    message: '删除成功!'
                        });
                })
                     
            });
        },
    },
    //用于存放要向网页中存放的数据
    data(){
        return{
            visible:false,
            comments:[],
            form:{
                type:"comment"
            }
        }
    },
    created(){
        //this为当前vue实例对象
        // vue实例创建完毕
        this.loadData();

    }
}
</script>

<style scoped>

</style>