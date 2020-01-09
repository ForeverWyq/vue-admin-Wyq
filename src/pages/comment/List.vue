<template>
    <div>
        评论管理
        <br><br>
        <!-- 按钮 -->
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- 按钮结束 -->
        <br>
        <!-- 表格 -->
        <el-table ref="multipleTable" :data="comments" tooltip-effect="dark" 
        style="width: 100%" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column label="编号" prop="id" fixed="left"></el-table-column>
            <el-table-column label="评论内容" prop="content" fixed="left"></el-table-column>
            <el-table-column label="评论时间" prop="commentTime" fixed="left"></el-table-column>
            <el-table-column label="订单id" prop="orderId"></el-table-column>
            <el-table-column label="操作" fixed="right">
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
                <el-form-item label="评论内容">
                    <el-input v-model="form.content"></el-input>
                </el-form-item>
                <el-form-item label="评论时间">
                    <el-input type="number" v-model="form.commentTime"></el-input>
                </el-form-item>
                <el-form-item label="订单id">
                    <el-input type="number" v-model="form.orderId"></el-input>
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
        // 重载评论数据
        loadData(){
            let url = "http://localhost:6677/comment/findAll"
            request.get(url).then((response)=>{
                this.comments = response.data;
            })
        },
        // 录入评论信息
        toAddHandler(){
            this.title="录入评论信息"
            // 重置
            this.form={}
            this.visible=true;
        },
        // 修改评论信息
        toUpdateHandler(row){
            this.title="修改评论信息"
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
                let url = "http://localhost:6677/comment/deleteById?id="+id;
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
            // 通过request与后台交互，并且携带参数
            let url = "http://localhost:6677/comment/saveOrUpdate"
            // 前端向后台发送请求，完成数据的保存
            request({
                url,
                method:"POST",
                // 告诉给后台我的请求体中放的是查询字符串
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                // 请求体中的数据，将this.form转换为查询字符串发送给后台
                data:querystring.stringify(this.form)
            }).then((response)=>{
                // 请求结束，模态框关闭
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
        closeModalHandler(){
            this.visible=false;
        },
        handleSelectionChange(val) {
            this.multipleSelection = val;
        }
    },
    //用于存放要向网页中显示的数据
    data(){
        return{
            title:"",
            visible:false,
            comments:[],
            form:{}
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