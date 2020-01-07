<template>
    <div>
        订单管理
        <br><br>
        <!-- 选项卡 -->
        <el-tabs v-model="params.status" @tab-click="loadData">
            <el-tab-pane label="全部" name="全部"></el-tab-pane>
            <el-tab-pane label="待派单" name="待派单"></el-tab-pane>
            <el-tab-pane label="待接单" name="待接单"></el-tab-pane>
            <el-tab-pane label="待服务" name="待服务"></el-tab-pane>
            <el-tab-pane label="待确认" name="待确认"></el-tab-pane>
            <el-tab-pane label="已完成" name="已完成"></el-tab-pane>
        </el-tabs>
        <!-- 选项卡结束 -->
        <!-- 表格 -->
        <el-table :data="orders.list">
            <el-table-column label="订单编号" prop="id"></el-table-column>
            <el-table-column label="下单时间" width="200" prop="orderTime"></el-table-column>
            <el-table-column label="总价" prop="total"></el-table-column>
            <el-table-column label="状态" prop="status"></el-table-column>
            <el-table-column label="顾客Id" prop="customerId"></el-table-column>
            <el-table-column label="员工Id" prop="waiterId"></el-table-column>
            <el-table-column label="地址Id" prop="addressId"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="javascript:void(0)">详情</a>
                    <a href="" v-if="slot.row.status==='待派单'" @click.prevent="toSendOrderHandler(slot.row)">派单</a>
                </template>
            </el-table-column>
        </el-table>
        <!-- 表格结束 -->
        <!-- 分页 -->
        <el-pagination
        hide-on-single-page="true"
        layout="prev, pager, next"
        :total="orders.total"
        @current-change="pageChageHandler">
        </el-pagination>
        <!-- 分页结束 -->
        <!-- 模态框 -->
        <el-dialog title="派单" :visible.sync="visible" width="50%">
            <div style="paddingLeft:2em">
              <p> <strong>订单编号: </strong> {{form.id}} </p> <br>
              <p> <strong>订单总价: </strong> {{form.total}} </p> <br>
              <p> <strong>下单时间: </strong> {{form.orderTime}} </p> <br>
              <p>
                <strong>派送员工:</strong>
                <el-select v-model="waiterId" placeholder="请选择">
                    <el-option v-for="e in employees" :key="e.id"
                    :label="e.realname" :value="e.id">
                </el-option>
                </el-select>
              </p>
            </div>
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
        // 加载订单信息
        loadData(){
            let url = "http://localhost:6677/order/queryPage"
            if(this.params.status ==="全部"){
                delete this.params.status;
            }
            request({
                url,
                method:"post",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.params)
            }).then((response)=>{
                // orders为一个对象，其中包含了分页信息，以及列表信息
                this.orders=response.data
            })
        },
        // 当分页中当前页改变时执行
        pageChageHandler(page){
            this.params.page=page-1;
            this.loadData();
        },
        // 派单
        toSendOrderHandler(row){
            // 模态框显示当前订单信息
            this.form = row;
            this.visible = true;
        },
        closeModalHandler(){
            this.visible = false;
        },
        // 加载员工信息
        loadEmployees(){
            let url="http://localhost:6677/waiter/findAll";
            request.get(url).then(response=>{
                this.employees=response.data;
            })
        },
        submitHandler(){
            let url = "http://localhost:6677/order/sendOrder";
            request({
                url,
                method:"GET",
                params:{
                orderId:this.form.id,
                waiterId:this.waiterId
                }
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
        }
    },
    //用于存放要向网页中显示的数据
    data(){
        return{
            visible:false,
            orders:{},
            form:{},    // 当前订单
            params:{
                page:0,
                pageSize:10
            },
            employee:[],
            waiterId:null   // 选中的员工
        }
    },
    created(){
        // vue实例创建完毕
        this.loadData();
        // 加载员工信息
        this.loadEmployees();
    }
}
</script>

<style scoped>

</style>