<template>
    <div>
        产品管理
        <br><br>
        <!-- 按钮 -->
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量审核</el-button>
        <!-- 按钮结束 -->
        <br>
        <!-- 表格 -->
        <el-table :data="products">
            <el-table-column label="编号" prop="id" fixed="left"></el-table-column>
            <el-table-column label="产品名称" prop="name" fixed="left"></el-table-column>
            <el-table-column label="价格" prop="price" fixed="left"></el-table-column>
            <el-table-column label="描述" prop="description"></el-table-column>
            <el-table-column label="所属栏目" prop="categoryId"></el-table-column>
            <el-table-column label="操作" fixed="right">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">
                        <i class="el-icon-delete"></i></a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">
                        <i class="el-icon-edit"></i></a>
                    <a href="" @click.prevent="">详情</a>
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
                <el-form-item label="产品名称">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="价格">
                    <el-input type="number" v-model="form.price"></el-input>
                </el-form-item>
                <el-form-item label="所属栏目">
                    <el-select v-model="form.status" placeholder="请选择">
                        <el-option v-for="item in options"
                            :key="item.value" :label="item.name"
                            :value="item.id">
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="介绍">
                    <el-input type="textarea" :autosize="{ minRows: 2, maxRows: 4}"
                    placeholder="请输入内容" v-model="form.description">
                    </el-input>
                </el-form-item>
                <el-form-item label="上传图片">
                <el-upload
                class="upload-demo"
                action="http://134.175.154.93:6677/file/upload"
                :on-success="uploadSuccessHandler"
                :file-list="fileList"
                list-type="picture">
                <el-button size="small" type="primary">点击上传</el-button>
                <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
                </el-upload>
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
        // 上传成功处理
        uploadSuccessHandler(response){
            let photo = "http://134.175.154.93:8888/group1/"
            +response.data.id
            // 将图片地址设置到form中，便于一起提交到后台
            this.form.photo = photo;
        },
        // 重载栏目数据
        loadData(){
            let url = "http://localhost:6677/product/findAll"
            request.get(url).then((response)=>{
                this.products = response.data;
            })
        },
        loadcategry(){
            let url = "http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{
                this.options = response.data;
            })
        },
        // 录入栏目信息
        toAddHandler(){
            this.title="添加产品信息"
            this.form={};
            this.fileList=[];
            this.visible=true;
        },
        // 修改栏目信息
        toUpdateHandler(row){
            this.title="修改产品信息"
            this.form=row;
            this.fileList=[];
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
                let url = "http://localhost:6677/product/deleteById?id="+id;
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
            let url = "http://localhost:6677/product/saveOrUpdate"
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
        }
    },
    //用于存放要向网页中显示的数据
    data(){
        return{
            title:"",
            visible:false,
            products:[],
            options: [],
            form:{},
            fileList:[]
        }
    },
    created(){
        // vue实例创建完毕
        // 读数据
        this.loadData();
        // 赋值
        this.loadcategry();
    }
}
</script>

<style scoped>

</style>