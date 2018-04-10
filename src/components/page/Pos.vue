<template>
  <div class="pos">
    <div>
      <el-row >
          <el-col :span='7' class="pos-order" id='orderlist'>
            <el-tabs type="card">
              <el-tab-pane label="点餐">
                <el-table :data='tabledata' border style='width:100%'>
                  <el-table-column prop="goodsName" label="商品名称"  ></el-table-column>
                  <el-table-column prop="count" label="数量" width="50"></el-table-column>
                  <el-table-column prop="price" label="金额" width="70"></el-table-column>
                  <el-table-column  label="操作" width="100" fixed="right">
                    <template slot-scope="scope">
                      <el-button type="text" size="small" @click="delsingle(scope.row)">删除</el-button>
                      <el-button type="text" size="small" @click="addOrderlist(scope.row)">增加</el-button>
                    </template>                  
                  </el-table-column>
                </el-table>
                
                <div class="totalDiv">
                  <small>数量：</small>
                  <strong>{{totalCount}}</strong> &nbsp;&nbsp;&nbsp;&nbsp;
                  <small>总计：</small>
                  <strong>{{totalMoney}}</strong> 元
                </div>
                <div class="div-btn">
                  <el-button type="warning" size="medium">挂单</el-button>
                  <el-button type="danger" size="medium">删除</el-button>
                  <el-button type="success" size="medium">结账</el-button>
                </div>

              </el-tab-pane>
              <el-tab-pane label="挂单">
                挂单
              </el-tab-pane>
              <el-tab-pane label="外卖">
                外卖
              </el-tab-pane>
            </el-tabs>
          </el-col>
            <!--商品展示-->
          <el-col :span="17">
            <div class="often-goods">
              <div class="title">常用商品</div>
              <div class="often-goods-list">
                <ul>
                  <li v-for="v in oftenGoods" @click="addOrderlist(v)">
                    <span>{{v.goodsName}}</span>
                    <span class="o-price">${{v.price}}</span>
                  </li>
                </ul>              
              </div>
            </div>

            <div class="good-types">
                <el-tabs type="border-card">
                    <el-tab-pane label="汉堡" class="firstgt">                      
                      <ul class='cookList'>
                          <li v-for="item in type0Goods" @click="addOrderlist(item)">
                              <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
                              <span class="foodName">{{item.goodsName}}</span>
                              <br>
                              <span class="foodPrice">￥{{item.price}}元</span>
                          </li>
                      </ul>
                    </el-tab-pane>
                    <el-tab-pane label="小食">
                      <ul class='cookList'>
                          <li v-for="item in type1Goods" @click="addOrderlist(item)">
                              <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
                              <span class="foodName">{{item.goodsName}}</span>
                              <br>
                              <span class="foodPrice">￥{{item.price}}元</span>
                          </li>
                      </ul>                        
                    </el-tab-pane>
                    <el-tab-pane label="饮料">
                      <ul class='cookList'>
                          <li v-for="item in type2Goods" @click="addOrderlist(item)">
                              <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
                              <span class="foodName">{{item.goodsName}}</span>
                              <br>
                              <span class="foodPrice">￥{{item.price}}元</span>
                          </li>
                      </ul>                        
                    </el-tab-pane>
                    <el-tab-pane label="套餐">
                      <ul class='cookList'>
                          <li v-for="item in type3Goods" @click="addOrderlist(item)">
                              <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
                              <span class="foodName">{{item.goodsName}}</span>
                              <br>
                              <span class="foodPrice">￥{{item.price}}元</span>
                          </li>
                      </ul>                        
                    </el-tab-pane>           
                </el-tabs>                           
            </div>
          </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
  import axios from 'axios'
  export default {
    name:"Pos",
    data(){
      return{
        tabledata:[],
        oftenGoods:[],
        type0Goods:[],
        type1Goods:[],
        type2Goods:[],
        type3Goods:[],
        totalMoney: 0, //订单总价格
        totalCount: 0  //订单商品总数量
      }
    },
    methods:{
      addOrderlist(good){
        //是否已经在列表中
        this.totalCount = 0; //汇总数量清0
        this.totalMoney = 0;
        let isHave = false;
        for(let i=0;i<this.tabledata.length;i++){
          if (this.tabledata[i].goodsId == good.goodsId){
            isHave = true
          }
        }
        //根据判断写逻辑
        if(isHave){
          //存在就加数量
          let arr = this.tabledata.filter(o=>o.goodsId==good.goodsId)
          arr[0].count++
        }else{
          let newGoods = {goodsId: good.goodsId,goodsName:good.goodsName, price: good.price, count: 1 }
          this.tabledata.push(newGoods)
        }
        this.getsum()
      },
      //删除单独
      delsingle(good){
        this.tabledata=this.tabledata.filter(o=>o.goodsId != good.goodsId)
        this.getsum()
      },
      getsum(){
            this.totalCount = 0;
            this.totalMoney = 0;
            if (this.tabledata) {
                this.tabledata.forEach((element) => {
                    this.totalCount += element.count;
                    this.totalMoney = this.totalMoney + (element.price * element.count);
                });
            }
      }
    },
    mounted:function(){
      var orderHeight=document.body.clientHeight
      document.getElementById('orderlist').style.height = orderHeight+'px'
    },
    created(){
      //常用商品
      axios.get('http://jspang.com/DemoApi/oftenGoods.php')
      .then(res=>{
        // console.log(res);
        this.oftenGoods = res.data
      })
      .catch(err=>{
        console.log(err)
      })
      //类型商品
      axios.get("http://jspang.com/DemoApi/typeGoods.php")
      .then(res=>{
        // console.log(res)
        this.type0Goods=res.data[0]
        this.type1Goods=res.data[1]
        this.type2Goods=res.data[2]
        this.type3Goods=res.data[3]
      })
      .catch(err=>{
        console.log(err)
      })
    }
  }
</script>

<style lang="less" scoped>
.pos-order{
  background:#f9fafc;
  border-right:1px solid #c0ccda;
  height:100%;
}
.div-btn{
  margin-top:10px
}
.good-types{
  clear:both;
  border-top:none;
}
 .title{
       height: 20px;
       border-bottom:1px solid #D3DCE6;
       background-color: #F9FAFC;
       padding:10px;
   }
   .often-goods-list{
    border-bottom: 1px solid #C0CCDA;
    height: auto;
    overflow: hidden;
    padding-bottom: 10px;
    background-color: #F9FAFC;
   }
   .often-goods-list ul li{
      list-style: none;
      float:left;
      border:1px solid #E5E9F2;
      padding:10px;
      margin:5px;
      background-color:#fff;
   }
  .o-price{
      color:#58B7FF; 
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
    font-size: 16px;    
    padding-left: 10px;
    color: brown;
}

.foodPrice {
    font-size: 16px;
    padding-left: 10px;
    padding-top: 10px;
}
.el-tabs__nav-scroll{
  margin-left:20px;
}
</style>
