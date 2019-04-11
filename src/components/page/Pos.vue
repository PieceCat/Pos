<template>
    <div class="pos">
        <el-row>
            <el-col :span='7' id="order-list" class="pos-order">
                <el-tabs>
                    <el-tab-pane label="点餐">
                        <el-table  :data="tableData" border style="width: 100%">
                            <el-table-column prop="goodsName" label="商品名称"></el-table-column>
                            <el-table-column prop="count" label="数量" width="50"></el-table-column>
                            <el-table-column prop="price" label="金额" width="70"></el-table-column>
                            <el-table-column label="操作" width="100" fixed="right">
                                <template slot-scope="scope">
                                    <el-button type="text" size="small" @click="delSingerGoods(scope.row)">删除</el-button>
                                    <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                                </template>
                            </el-table-column>
                        </el-table>

                         <div class="totalDiv">
                            <small>数量：</small>
                            <strong>{{totalCount}}</strong> &nbsp;&nbsp;&nbsp;&nbsp;
                            <small>总计：</small>
                            <strong>{{totalMoney}}</strong> 元
                        </div>

                        <el-row class="order-btn">
                            <el-button type="warning">挂单</el-button>
                            <el-button type="danger" @click="delAllGoods">删除</el-button>
                            <el-button type="success" @click="checkout()">结账</el-button>
                        </el-row>
                    </el-tab-pane>
                    <el-tab-pane label="挂单">
                        
                    </el-tab-pane>
                    <el-tab-pane label="外卖">

                    </el-tab-pane>
                </el-tabs>
                
            </el-col>
            <!--商品展示-->
            <el-col :span="17">
                <div class="often-goods">
                    <div class="title">常用商品</div>
                    <div class="often-goods-list">
                        <ul>
                            <li v-for="item in oftenGoods" :key="item.id" @click="addOrderList(item)">
                                <span>{{item.goodsName}}</span>
                                <span class="o-price">￥{{item.price}}元</span>
                            </li>
                        </ul>
                    </div>
                </div>
                <div class="goods-type">
                     <el-tabs>
                        <el-tab-pane label="汉堡">
                            <div class="cookList">
                                <li v-for="item in type0Goods" :key="item.id">
                                    <span class="foodImg"><img :src="item.goodsImg" width="100%" alt=""></span>
                                    <span class="foodName">{{item.goodsName}}</span>
                                    <span class="foodPrice">￥{{item.price}}元</span>
                                </li>
                            </div>
                        </el-tab-pane>
                        <el-tab-pane label="小食">
                            <div class="cookList">
                                <li v-for="item in type1Goods" :key="item.id">
                                    <span class="foodImg"><img :src="item.goodsImg" width="100%" alt=""></span>
                                    <span class="foodName">{{item.goodsName}}</span>
                                    <span class="foodPrice">￥{{item.price}}元</span>
                                </li>
                            </div>
                        </el-tab-pane>
                        <el-tab-pane label="饮料">
                            <div class="cookList">
                                <li v-for="item in type2Goods" :key="item.id">
                                    <span class="foodImg"><img :src="item.goodsImg" width="100%" alt=""></span>
                                    <span class="foodName">{{item.goodsName}}</span>
                                    <span class="foodPrice">￥{{item.price}}元</span>
                                </li>
                            </div>
                        </el-tab-pane>
                        <el-tab-pane label="套餐">
                            <div class="cookList">
                                <li v-for="item in type3Goods" :key="item.id">
                                    <span class="foodImg"><img :src="item.goodsImg" width="100%" alt=""></span>
                                    <span class="foodName">{{item.goodsName}}</span>
                                    <span class="foodPrice">￥{{item.price}}元</span>
                                </li>
                            </div>
                        </el-tab-pane>
                    </el-tabs>
                </div>
            </el-col>
        </el-row>
    </div>
</template>
<script>
import Vue from 'vue'
import axios from 'axios'

axios.defaults.baseURL = ' https://www.easy-mock.com/mock/5b693c0d8733b43c01e267dc/example/'
Vue.prototype.axios = axios;
Vue.prototype.$http = axios;
export default {
    name: 'pos',
    data(){
        return {
            tableData: [],
            oftenGoods:[],
            type0Goods:[],
            type1Goods:[],
            type2Goods:[],
            type3Goods:[],
            totalCount:0,
            totalMoney:0
        }
    },
    created(){
        axios
        .get('oftenGoods')
        .then((response)=>{
            if(response.status===200){
                this.oftenGoods=response.data;
                console.log(this.oftenGoods)
            }
        })
        .catch(error=>{
            console.log(error)
        })
        axios
        .get('typeGoods')
        .then((response)=>{
            if(response.status===200){
                this.type0Goods = response.data[0]
                this.type1Goods = response.data[1]
                this.type2Goods = response.data[2]
                this.type3Goods = response.data[3]
            }
        })
        .catch(error=>{
            console.log(error)
        })
    },
    mounted(){
        var orderHeight = document.body.clientHeight
        document.getElementById('order-list').style.height = orderHeight+'px'
    },
    methods: {
        addOrderList(goods){
            this.totalCount = 0
            this.totalMoney = 0
            let isHave = false
            for(let i=0;i<this.tableData.length;i++){
                if(this.tableData[i].goodsId == goods.goodsId){
                    isHave = true
                }
            }
            if(isHave){
                let arr = this.tableData.filter(o => o.goodsId == goods.goodsId)
                arr[0].count++
            }else{
                let newGoods= {goodsId:goods.goodsId,goodsName: goods.goodsName,price:goods.price,count:1}
                this.tableData.push(newGoods)
            }

            this.tableData.forEach((element)=>{
                this.totalCount += element.count
                this.totalMoney = this.totalMoney+(element.price*element.count)
            })
        },
        delSingerGoods(goods){
            this.tableData = this.tableData.filter(o=> o.goodsId!=goods.goodsId)
        },
        getAllMoney(){
            this.totalCount = 0
            this.totalMoney = 0
            if(this.tableData){
                this.tableData.forEach((element)=>{
                    this.totalCount += element.count
                    this.totalMoney = this.totalMoney+(element.price*element.count)
                })
            }
        },
        delAllGoods(){
            this.tableData = []
            this.totalCount = 0
            this.totalMoney = 0
        },
        checkout() {
            if (this.totalCount!=0) {
                this.tableData = [];
                this.totalCount = 0;
                this.totalMoney = 0;
                this.$message({
                    message: '结账成功，感谢你又为店里出了一份力!',
                    type: 'success'
                });
            }else{
                this.$message.error('不能空结。老板了解你急切的心情！');
            }
        }
    }
}
</script>

<style scoped>
.pos {
    font-size: 12px;
}
.pos-order {
    background-color: #F9FAFC;
    border-right: 1px solid #C0CCDA;
}
.order-btn {
    margin-top: 10px;
    text-align: center;
}
.title {
    height: 20px;
    border-bottom: 1px solid #D3DCE6;
    background-color: #F9FAFC;
    padding: 10px;
}
.often-goods-list ul li {
    list-style: none;
    float: left;
    border: 1px solid #E5E9F2;
    padding: 10px;
    margin: 5px;
    background-color: #fff;
    cursor: pointer;
}
.goods-type {
    clear: both;
}
.o-price {
    color: #58B7FF;
}
.often-goods-list {
    border-bottom: 1px solid #C0CCDA;
    height: auto;
    overflow: hidden;
    padding-bottom: 10px;
    background-color: #F9FAFC;
}
.cookList li {
    list-style: none;
    width: 23%;
    border: 1px solid #E5E9F2;
    height: auot;
    overflow: hidden;
    background-color: #fff;
    padding: 2px;
    float: left;
    margin: 2px;
    cursor: pointer;
}
.cookList li span {
    display: block;
    float: left;
}
.foodImg {
    width: 40%;
}
.foodName {
    font-size: 18px;
    padding-left: 10px;
    color: brown;
    width: 50%;
}
.foodPrice {
    font-size: 16px;
    padding-left: 10px;
    padding-top: 10px;
}
.totalDiv {
    height: auot;
    overflow: hidden;
    text-align: right;
    font-size: 16px;
    background-color: #fff;
    border-bottom: 1px solid #E5E9F2;
    padding: 10px;
}
</style>
