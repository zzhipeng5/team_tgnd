<template>
<!-- 产品管理 -->
    <div>
        <!--按钮-->
        <el-button size="small" type="success" @click="toAddHandler">添加</el-button>
        <el-button size="small" type="danger">批量删除</el-button>
        <!--/按钮-->
        <!--表格-->
        <el-table :data="product"> 
            <el-table-column fixed="left" prop="categoryId" label="分类编号"></el-table-column>
    <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
    <el-table-column fixed="left" prop="name" label="产品名"></el-table-column>
    <el-table-column fixed="left" prop="price" label="价格"></el-table-column>
    <el-table-column prop="categoryId" label="所属产品"></el-table-column>
    <el-table-column width="120" prop="description" label="产品说明"></el-table-column>
    <el-table-column width="200" prop="photo" label="产品照片">
        <template   slot-scope="scope">            
                    <img :src="scope.row.photo"  min-width="70" height="70" />
                 </template>   
    </el-table-column>
    <el-table-column fixed="right" label="操作">
        <template v-slot="slot">
                    <!--阻止默认跳转-->
                    <a href="" @click.prevent="toUpdataHandler(slot.row)">修改</a>
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
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
            title="录入产品信息"
            :visible.sync="visible"
            width="60%">
            <el-form :model="form" label-width="80px">
            <el-form-item label="产品名">
         <el-input type="name" v-model="form.name"/>
      </el-form-item>
             <el-form-item label="所属栏目">
            <el-select v-model="form.categoryId">
                <el-option 
                    v-for="item in options" 
                    :key="item.id"
                    :label="item.name"
                    :value="item.id"></el-option>
            </el-select>
        </el-form-item>
            <el-form-item label="产品说明">
          <el-input type="textarea" v-model="form.description"/>
        </el-form-item>
            <el-form-item label="价格">
          <el-input v-model="form.price"/>
        </el-form-item>
                    <el-form-item label="产品照片">
          <el-input  v-model="form.photo"/>
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
        loadCategry(){
    let url = "http://localhost:6677/category/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到product中,this指向外部函数的this
            this.options=response.data;
    })
        },
        loadData(){
    let url = "http://localhost:6677/product/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到product中,this指向外部函数的this
            this.product=response.data;
    })
        },
        
        submitHandler(){
            //this.form 对象---字符串-->后台
            //通过request与后台进行交互，并且要携带参数
            let url ="http://localhost:6677/product/saveOrUpdate"
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
                type:"product"
            }
            this.visible=true;
        },
        closeModelHandler(){
            this.visible=false;
        },
        toUpdataHandler(row){
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
                let url = "http://localhost:6677/product/deleteById?id="+id;
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
            product:[],
            options:[],
            form:{
                type:"product"
            }
        }
    },
    created(){
        //this为当前vue实例对象
        // vue实例创建完毕
        this.loadData();
        //加载栏目信息
        this.loadCategry();
    }
}
</script>

<style scoped>

</style>