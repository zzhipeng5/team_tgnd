<template>
<!-- 订单管理 -->
    <div> 
        <!--表格-->
        <el-table :data="order"> 
            <el-table-column prop="id" label="订单编号"></el-table-column>
            <el-table-column prop="orderTime" label="下单时间"></el-table-column>
            <el-table-column prop="total" label="总价"></el-table-column>
            <el-table-column prop="status" label="状态"></el-table-column>
            <el-table-column prop="customerId" label="顾客id"></el-table-column>
            
            <el-table-column label="操作">
                <template v-slot="slot">
                    <!--阻止默认跳转-->
                <a href="" @click.prevent>详情 </a>    
      
                    
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
            <el-form :model="form" label-width="80px">
                <el-form-item label="订单编号">
                    <el-input v-model="form.id"></el-input>
                </el-form-item>
                 <el-form-item label="下单时间">
                    <el-input  v-model="form.orderTime"></el-input>
                </el-form-item>
                 <el-form-item label="总价">
                    <el-input v-model="form.total"></el-input>
                </el-form-item>
                 <el-form-item label="状态">
                    <el-input v-model="form.status"></el-input>
                <el-form-item label="顾客id">
                    <el-input v-model="form.customerId"></el-input>
                </el-form-item>
                </el-form-item>
                
            </el-form>  

            <span slot="footer" class="dialog-footer">
            <el-button @click="closeModelHandler" size="small">取 消</el-button>
            <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
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
    let url = "http://localhost:6677/order/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到order中,this指向外部函数的this
            this.order=response.data;
    })
        },
        
        submitHandler(){
            //this.form 对象---字符串-->后台
            //通过request与后台进行交互，并且要携带参数
            let url ="http://localhost:6677/order/saveOrUpdate"
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
                type:"order"
            }
            this.visible=true;
        },
        closeModelHandler(){
            this.visible=false;
        },
        toUpdataHandler(row){
            this.title="修改员工信息";
            this.visible=true;
            this.form=row;
        },
       toDeletHandler(id) {
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
                //调用后台接口，完成删除操作
                let url = "http://localhost:6677/order/deleteById?"+id;
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
        }
    },
    //用于存放要向网页中存放的数据
    data(){
        return{
            visible:false,
            order:[],
            form:{
                type:"order"
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
