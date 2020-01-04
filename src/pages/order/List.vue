<template>
    <div>
        订单管理
        <br><br>
        <!-- 按钮 -->
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <br>
        <!-- 按钮结束 -->
        <!-- 表格 -->
        <el-table :data="order">
            <el-table-column label="订单编号" prop="id"></el-table-column>
            <el-table-column label="下单时间" prop="orderTime"></el-table-column>
            <el-table-column label="总价" prop="total"></el-table-column>
            <el-table-column label="状态" prop="status"></el-table-column>
            <el-table-column label="顾客Id" prop="customerId"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">
                        <i class="el-icon-delete"></i></a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">
                        <i class="el-icon-edit"></i></a>
                </template>
            </el-table-column>
        </el-table>
        <!-- 表格结束 -->
        <!-- 分页 -->
        <!-- <el-pagination
            layout="prev, pager, next"
            :total="50">
        </el-pagination> -->
        <!-- 分页结束 -->
        <!-- 模态框 -->
        <el-dialog
          :title="title"
          :visible.sync="visible"
          width="60%">
          <el-form :model="form" label-width="80px">
                <el-form-item label="下单时间">
                    <el-input v-model="form.orderTime"></el-input>
                </el-form-item>
                <el-form-item label="总价">
                    <el-input  v-model="form.total"></el-input>
                </el-form-item>
                <el-form-item label="状态">
                    <el-input v-model="form.status"></el-input>
                </el-form-item>
                <el-form-item label="顾客Id">
                    <el-radio-group v-model="form.customerId">
                        <el-input v-model="form.realname"></el-input>
                    </el-radio-group>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button size="small"
                @click="closeModalHandler">取 消</el-button>
                <el-button type="primary" size="small"
                @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
        <!-- 模态框结束 -->
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    // 用于存放网页中需要调用的方法
    methods:{
        loadData(){
            let url = "http://localhost:6677/order/findAll"
            request.get(url).then((response)=>{
                this.order = response.data;
            })
        },
        // 录入订单信息
        toAddHandler(){
            this.title="录入订单信息"
            // 重置
            this.form={
                type:"order"
            }
            this.visible=true;
        },
        // 修改订单信息
        toUpdateHandler(row){
            // 模态框表单中显示当前行的信息
            this.title="修改订单信息"
            this.form=row;
            this.visible=true;
        },
        // 删除
        toDeleteHandler(id){
            // 确认？
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
                // 调用后台接口完成删除操作
                let url = "http://localhost:6677/order/deleteById?id="+id;
                request.get(url).then((response)=>{
                    // 刷新数据
                    this.loadData();
                    // 提示结果
                    this.$message({    
                        type: 'success',
                       message: '删除成功!'
                    });
                })
            })
            
        },
        // 录入界面中点击确定调用的保存方法
        submitHandler(){

        },
        closeModalHandler(){
            this.visible=false;
        }
    },
    //用于存放要向网页中显示的数据
    data(){
        return{
            title:"",
            visible:false,
            order:[],
            form:{
                type:"order"
            }
        }
    },
    created(){
        // vue实例创建完毕
        this.loadData();
    }
}
</script>

<style scoped>

</style>