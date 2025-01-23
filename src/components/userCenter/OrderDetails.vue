<!-- 這是給登入會員 看的 訂單列表
     OrderDetails.vue 
-->
<template>
  <div class="container">
    <div class="tableContainer">
      <div class="title">訂單與明細查詢</div>
      <div v-for="order in orders" :key="'order-' + order.orderId" class="order-block">
        <!-- 訂單主要信息 -->
        <table class="table">
          <thead>
            <tr>
              <th>會員編號</th>
              <th>訂單編號</th>
              <th>建立日期</th>
              <th>總金額</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>{{ order.userId }}</td>
              <td>{{ order.orderId }}</td>
              <td>{{ order.createdDate }}</td>
              <td>{{ order.totalAmount }}</td>
            </tr>
          </tbody>
        </table>

        <!-- 該訂單的明細 -->
        <div class="details-section">
          <table class="detailsTable">
            <thead>
              <tr>
                <th>商品編號</th>
                <th>商品名稱</th>
                <th>數量</th>
                <th>金額</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in order.orderItemList" :key="'item-' + item.orderItemId">
                <td>{{ item.productId }}</td>
                <td>{{ item.productName }}</td>
                <td>{{ item.quantity }}</td>
                <td>{{ item.amount }}</td>
              </tr>
            </tbody>
          </table>
        </div>
        <div class="order-separator"></div>
      </div>
    </div>
  </div>
</template>


<script setup>
import { ref, onMounted } from 'vue';
import axios from "axios";

const userId = localStorage.getItem('userId');

// 儲存訂單資料
const orders = ref([]);

const params = {userId: userId};

// 請求後端API
const fetchOrders = async () => {
  try {
    // 取得存儲在 localStorage 中的 token
    const token = localStorage.getItem('token');

    // 設定 Axios 請求的 headers
    if (token) {
      const response = await axios.get(
        `http://localhost:8080/orders/getAllOrders`,
        {
          params:params,
          headers: {
            Authorization: `Bearer ${token}`,  // 加入 JWT token
          }
        }
      );

      console.log("response.data回傳為", JSON.stringify(response.data, null, 2));
      orders.value = response.data.list; // 設置訂單列表
    } else {
      console.error("未獲取到 token，無法進行請求");
    }
  } catch (error) {
    console.error('fetching orders方法錯誤:', error);
  }
};
// 初始化時調用 API
onMounted(() => {
  fetchOrders();
});

</script>

<style scoped>
.container {
  margin: 20px auto;
}

.tableContainer {
  text-align: center;
}

.table {
  margin: 0 auto;
  width: 700px;
  border-collapse: collapse;
  text-align: center;
  border: 2px solid lightgray;
}

.table th,
.table td {
  border: 2px solid lightgray;
  padding: 4px;
}

.table thead th {
  background: rgb(255, 255, 100, 0.7);
  font-weight: bold;
}

.title {
  margin-bottom: 20px;
  font-size: 24px;
  font-weight: bold;
}

.role {
  min-width: 80px;
}
</style>
