<template>
  <div class="pos">
    <el-row>
      <!-- center -->
      <el-col :span="7" class="pos-order" id="order-list">
        <el-tabs type="border-card">
          <el-tab-pane label="点餐">
            <el-table :data="tableData" border style="width:100%">
              <el-table-column prop="goodsName" label="商品名称"></el-table-column>
              <el-table-column prop="count" label="数量" width="50"></el-table-column>
              <el-table-column prop="price" label="金额" width="70"></el-table-column>
              <el-table-column fixed="right" label="操作" width="100">
                <template slot-scope="scope">
                  <el-button @click="addOrderList(scope.row)" type="text" size="small">增加</el-button>
                  <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="resPrice">
              <p>
                数量：
                <span>{{goodsCount}}</span>
              </p>
              <p>
                金额：
                <span>{{totalMoney}}</span>
              </p>
            </div>
            <div class="sub-btn">
              <!-- <el-button type="warning">挂单</el-button> -->
              <el-button type="danger" @click="delAllGoods">删除</el-button>
              <el-button type="success" @click="checkout">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单">挂单</el-tab-pane>
          <el-tab-pane label="外卖">外卖</el-tab-pane>
        </el-tabs>
      </el-col>
      <!-- right -->
      <el-col :span="17">
        <!-- right-top -->
        <div class="often-goods">
          <div class="title">商品列表</div>
          <div class="often-goods-list">
            <ul class="cookList">
              <li v-for="goods in oftenGoods" :key="goods.goodsId" @click="addOrderList(goods)">
                <span>{{goods.goodsName}}</span>
                <span class="o-price">￥{{goods.price}}元</span>
              </li>
            </ul>
          </div>
        </div>
        <div class="goods-type">
          <el-tabs type="card">
            <el-tab-pane label="汉堡">
              <ul class="cookList">
                <li
                  class="listBox"
                  v-for="goods in type0Goods"
                  :key="goods.goodsId"
                  @click="addOrderList(goods)"
                >
                  <div class="foodImg">
                    <img :src="goods.goodsImg" width="100%" />
                  </div>
                  <div class="foodInfo">
                    <p class="foodName">{{goods.goodsName}}</p>
                    <p class="foodPrice">￥{{goods.price}}元</p>
                  </div>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <ul class="cookList">
                <li
                  class="listBox"
                  v-for="goods in type1Goods"
                  :key="goods.goodsId"
                  @click="addOrderList(goods)"
                >
                  <div class="foodImg">
                    <img :src="goods.goodsImg" width="100%" />
                  </div>
                  <div class="foodInfo">
                    <p class="foodName">{{goods.goodsName}}</p>
                    <p class="foodPrice">￥{{goods.price}}元</p>
                  </div>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <ul class="cookList">
                <li
                  class="listBox"
                  v-for="goods in type2Goods"
                  :key="goods.goodsId"
                  @click="addOrderList(goods)"
                >
                  <div class="foodImg">
                    <img :src="goods.goodsImg" width="100%" />
                  </div>
                  <div class="foodInfo">
                    <p class="foodName">{{goods.goodsName}}</p>
                    <p class="foodPrice">￥{{goods.price}}元</p>
                  </div>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <ul class="cookList">
                <li
                  class="listBox"
                  v-for="goods in type3Goods"
                  :key="goods.goodsId"
                  @click="addOrderList(goods)"
                >
                  <div class="foodImg">
                    <img :src="goods.goodsImg" width="100%" />
                  </div>
                  <div class="foodInfo">
                    <p class="foodName">{{goods.goodsName}}</p>
                    <p class="foodPrice">￥{{goods.price}}元</p>
                  </div>
                </li>
              </ul>
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
  name: "pos",
  data() {
    return {
      tableData: [],
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      goodsCount: 0,
      totalMoney: 0
    };
  },
  mounted: function() {
    var orderHeight = document.body.clientHeight;
    // console.log(orderHeight);
    document.getElementById("order-list").style.height = orderHeight + "px";
  },
  created: function() {
    // 获取商品价格
    axios
      .get(
        "https://www.easy-mock.com/mock/5d352b499642b83b85c561e0/goodsList/oftenGoods"
      )
      .then(res => {
        // console.log(res);
        if (res.status == 200) {
          this.oftenGoods = res.data;
        }
      })
      .catch(err => {
        console.log(err);
        alert("网络错误");
      });
    // 获取商品详情
    axios
      .get("https://www.easy-mock.com/mock/5d352b499642b83b85c561e0/goodsList/")
      .then(res => {
        // console.log(res);
        if (res.status == 200) {
          this.type0Goods = res.data[0];
          this.type1Goods = res.data[1];
          this.type2Goods = res.data[2];
          this.type3Goods = res.data[3];
        }
      })
      .catch(err => {
        console.log(err);
        alert("网络错误");
      });
  },
  methods: {
    // 增加商品
    addOrderList(goods) {
      // 判断是否这个商品已经存在于订单列表
      this.goodsCount = 0; //汇总数量清0
      this.totalMoney = 0;
      let isHave = false;
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId == goods.goodsId) {
          isHave = true;
        }
      }

      //根据isHave的值判断订单列表中是否已经有此商品
      if (isHave) {
        // filter它用于把Array的某些元素过滤掉，然后返回剩下的元素。
        //留下goodsId相同的元素
        let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
        arr[0].count++;
      } else {
        let newArr = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        };
        this.tableData.push(newArr);
      }
      // 增加商品
      this.getAllGoodsPrice();
    },
    // 删除商品
    delSingleGoods(goods) {
      // console.log(goods);
      this.tableData = this.tableData.filter(
        res => res.goodsId != goods.goodsId
      );
      this.getAllGoodsPrice();
    },
    //删除所有商品
    delAllGoods() {
      this.tableData = [];
      this.totalCount = 0;
      this.totalMoney = 0;
    },
    // 所有商品的汇总
    getAllGoodsPrice() {
      this.goodsCount = 0;
      this.totalMoney = 0;
      if (this.tableData) {
        // 计算数量及金额
        this.tableData.forEach(element => {
          this.goodsCount += element.count;
          this.totalMoney = this.totalMoney + element.price * element.count;
        });
      }
    },
    // 结账
    checkout() {
      if (this.goodsCount != 0) {
        this.tableData = [];
        this.goodsCount = 0;
        this.totalMoney = 0;
        this.$message({
          message: "结账成功，欢迎下次光临",
          type: "success"
        });
      } else {
        this.$message.error("结账错误，您的订单为空");
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.pos-order {
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
}
.resPrice {
  display: flex;
  justify-content: center;
  align-content: center;
  border: 1px solid #ebeef5;
  border-top: 0px;
}
.resPrice p {
  flex: 5;
}
.resPrice p span {
  color: red;
}

.sub-btn {
  margin-top: 10px;
}
.title {
  height: 20px;
  border-bottom: 1px soild #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
  text-align: left;
}

/* right */
.cookList li {
  cursor: pointer;
  list-style: none;
  border: 1px solid #e5e9f2;
  height: auot;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
}
.title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
}
.often-goods-list ul li {
  list-style: none;
  float: left;
  border: 1px solid #e5e9f2;
  padding: 10px;
  margin: 5px;
  background-color: #fff;
}
.o-price {
  color: red;
}
.goods-type {
  clear: both;
}
/* 右侧底部 */
.listBox {
  display: flex;
}
.foodImg {
  flex: 3;
}
.foodImg img {
  height: 100px;
  width: auto;
}
.foodInfo {
  padding: 10px;
  display: flex;
  justify-content: space-around;
  align-content: center;
  flex-direction: column;
  font-size: 12px;
}
.foodPrice {
  color: red;
}
</style>
