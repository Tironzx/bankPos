<template>
    <div class="pos">
        <el-row>
            <el-col :span="7" class="pos-order" id="order-list">
                <el-tabs style="padding-left:10px;">
                    <el-tab-pane label="点餐">
                        <el-table  :data="tableData" border width="100%">
                            <el-table-column align="center" prop="productName" label="商品名称"></el-table-column>
                            <el-table-column align="center" prop="count" label="数量"></el-table-column>
                            <el-table-column align="center" prop="price" label="单价"></el-table-column>
                            <el-table-column align="center" label="操作" fixed="right">
                                <template slot-scope="scope">
                                    <el-button type="text" size="small" @click="delproduct(scope.row)">删除</el-button>
                                    <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                                </template>
                            </el-table-column>
                        </el-table>
                        <div class="total">
                           <small>数量：</small>{{totalCount}} &nbsp; <small>金额：</small>{{totalMoney}}元
                        </div>
                        <div class="caozuo">    
                            <el-button type="warning" size="small">挂单</el-button>
                            <el-button type="danger" size="small" @click="delAllProduct">删除</el-button>
                            <el-button type="success" size="small" @click="checkOut">结账</el-button>
                        </div>
                    </el-tab-pane>
                    <el-tab-pane label="挂单">挂单</el-tab-pane>
                    <el-tab-pane label="外卖">外卖</el-tab-pane>
                </el-tabs>
            </el-col>
            <el-col :span="17">
                <div class="often-product">
                    <div class="product-title">常用商品</div>
                    <div class="product-list">
                        <ul>
                            <li v-for="(tmp,index) in foods" :key="index" @click="addOrderList(tmp)">
                                <span>{{tmp.goodsName}}</span> 
                                <span class="product-price">¥{{tmp.price}}元</span>
                            </li>
                        </ul>
                    </div>
                </div>
                <div class="foods-type">
                    <el-tabs>
                        <el-tab-pane label="汉堡">
                            <div>
                                <ul class='cookList'>
                                    <li v-for="(goods,index) in type0Goods" :key="index" @click="addOrderList(goods)">
                                        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                        <span class="foodName">{{goods.goodsName}}</span>
                                        <span class="foodPrice">￥{{goods.price}}元</span>
                                    </li>
                                </ul>
                            </div>
                        </el-tab-pane> 
                        <el-tab-pane label="小吃">
                            <div>
                                <ul class='cookList'>
                                    <li v-for="(goods,index) in type1Goods" :key="index" @click="addOrderList(goods)">
                                        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                        <span class="foodName">{{goods.goodsName}}</span>
                                        <span class="foodPrice">￥{{goods.price}}元</span>
                                    </li>
                                </ul>
                            </div>
                        </el-tab-pane>
                        <el-tab-pane label="饮料">
                            <div>
                                <ul class='cookList'>
                                    <li v-for="(goods,index) in type2Goods" :key="index" @click="addOrderList(goods)">
                                        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                        <span class="foodName">{{goods.goodsName}}</span>
                                        <span class="foodPrice">￥{{goods.price}}元</span>
                                    </li>
                                </ul>
                            </div>
                        </el-tab-pane>
                        <el-tab-pane label="套餐">
                            <div>
                                <ul class='cookList'>
                                    <li v-for="(goods,index) in type3Goods" :key="index" @click="addOrderList(goods)">
                                        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                        <span class="foodName">{{goods.goodsName}}</span>
                                        <span class="foodPrice">￥{{goods.price}}元</span>
                                    </li>
                                </ul>
                            </div>
                        </el-tab-pane>
                    </el-tabs>
                </div>
            </el-col>
        </el-row>
    </div>
</template>
<script>
import axios from "axios";
    export default {
        name:"pos",
        data(){
            return{
                tableData: [],
                foods:[],
                type0Goods:[],
                type1Goods:[],
                type2Goods:[],
                type3Goods:[],
                totalMoney:0,
                totalCount:0
            }
        },
        created:function(){
            axios.get("http://jspang.com/DemoApi/oftenGoods.php")
            .then(reponse=>{
                // console.log(reponse);
                this.foods=reponse.data;
            })
            .catch(error=>{
                console.log("error:网络错误，请刷新页面重试");
            });
            // 拉取分类商品
            axios.get("http://jspang.com/DemoApi/typeGoods.php")
            .then(reponse=>{
                this.type0Goods=reponse.data[0];
                this.type1Goods=reponse.data[1];
                this.type2Goods=reponse.data[2];
                this.type3Goods=reponse.data[3];
            })
            .catch(error=>{
                console.log("error:网络错误，请刷新页面重试");
            });
        },
        mounted:function(){
            let orderHeight=document.body.clientHeight;
            document.getElementById("order-list").style.height=orderHeight+"px";
        },
        methods:{
            addOrderList(goods){
                // console.log(goods);
                this.totalMoney=0;
                this.totalCount=0;
                // 判断商品是否已经存在于订单列表中
                let isHas=false;
                for(let i=0;i<this.tableData.length;i++){
                    if(this.tableData[i].goodsId==goods.goodsId){
                        isHas=true;
                    }
                }
                // 根据判断写业务逻辑
                if(isHas){
                    // 改变列表中商品的数量
                    let arr=this.tableData.filter(o=>o.goodsId=goods.goodsId);
                    arr[0].count++;
                }else{
                    let newGoods={goodsId:goods.goodsId,productName:goods.goodsName,price:goods.price,count:1}
                    this.tableData.push(newGoods);
                }
                // 计算汇总金额和数量
                this.getAllMoney();
            },
            // 删除单个商品
            delproduct(goods){
                this.tableData=this.tableData.filter(o=>o.goodsId!=goods.goodsId);
                this.getAllMoney();
            },
            // 计算汇总数量和金额
            getAllMoney(){
                this.totalCount=0;
                this.totalMoney=0;
                if(this.tableData){
                    this.tableData.forEach((element)=>{
                    this.totalCount+=element.count;
                    this.totalMoney=this.totalMoney+(element.price*element.count);
                })
                }
            },
            // 删除所有商品
            delAllProduct(){
                this.tableData=[];
                this.totalMoney=0;
                this.totalCount=0;
            },
            // 模拟结账
            checkOut(){
                if(this.totalCount!=0){
                    this.tableData=[];
                    this.totalMoney=0;
                    this.totalCount=0;
                    this.$message({
                        message:"结账成功，欢迎下次光临!",
                        type:"success"
                    })
                }else{
                    this.$message.error("您还没有选择商品")
                }
            }
        }
    }
</script>

<style scoped>
    .pos-order {
        background-color: #f9fafc;
        border-right: 1px solid #c0ccda;
    }
    .caozuo {
        margin-top: 10px;
    }

    .often-product {
        font-size: 16px;
    }
    .product-title {
        height: 20px;
        line-height: 20px;
        border-bottom: 1px solid #d3dce6;
        background-color: #f9fafc;
        padding: 20px;
        font-size: 18px;
        text-align: left;
    }
    .product-list:after {
        display: table;
        content: '';
        clear: both;
    }
    .product-list ul {
        list-style: none;
    }
    .product-list li {
        float: left;
        border: 1px solid #e5e9f2;
        padding: 10px;
        margin: 10px;
        background-color: #fff;
        border-radius: 10px;
        cursor: pointer;
    }
    .total {
        background-color: #fff;
        padding: 10px;
        border-bottom: 1px solid #d3d6ec;
        font-size: 20px;
    }
    .product-price {
        color: #5887ff;
    }
    .foods-type {
        padding-left: 10px;
    }
    .cookList li{
       list-style: none;
       width:23%;
       border:1px solid #E5E9F2;
       height: auot;
       overflow: hidden;
       background-color:#fff;
       padding: 2px;
       float:left;
       margin: 2px;
       cursor: pointer;
   }
   .cookList li span{
        display: block;
        float:left;
   }
   .foodImg{
       width: 40%;
   }
   .foodName{
       font-size: 18px;
       padding-left: 10px;
       color:brown;
 
   }
   .foodPrice{
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
   }
</style>

