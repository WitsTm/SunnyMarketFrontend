<template>
  <Header />
  <div class="cart-contain">
    <table v-if="items.length > 0" border="1">
      <thead>
        <tr>
          <th>
            全選
            <input
              type="checkbox"
              :checked="allSelected"
              @change="toggleAllSelection"
              class="allcheck"
            />
          </th>
          <th>圖片</th>
          <th>品名</th>
          <th>價格</th>
          <th>數量</th>
          <th>小計</th>
          <th>是否刪除</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in items" :key="item.id">
          <td>
            <input
              type="checkbox"
              :checked="item.selected"
              @change="toggleItemSelection(item.productId)"
              class="checkinput"
            />
          </td>
          <td>
            <img
              :src="item.imageUrl"
              alt="商品圖片"
              style="width: 100px; height: auto"
            />
          </td>
          <td>{{ item.productName }}</td>
          <td>${{ item.price }}</td>
          <td>
            <input
              class="numinput"
              type="number"
              v-model.number="item.quantity"
              @input="updateQuantity(item.productId, item.quantity)"
              @blur="setDefaultQuantity(item)"
              min="1"
            />
          </td>
          <td class="subtotal">${{ item.price * item.quantity }}</td>
          <td>
            <button
              @click="
                () => {
                  console.log('點刪除按鈕', item.productId);
                  removeItem(item.productId);
                }
              "
            >
              刪除商品
            </button>
          </td>
        </tr>
      </tbody>
    </table>
    <p v-else>購物車還沒有商品喔！</p>
    <div class="total">
      <p>📦 總品項 {{ totalQuantity }} 項</p>
      <p>🧾 總金額 NT$ {{ totalPrice }}</p>
    </div>
    <div class="bottom-btn">
      <button @click="clearCart">清空購物車</button>
      <button @click="checkOut">確認結帳</button>
    </div>
  </div>
  <TopButton />
  <CartBtn />
  <Footer />
</template>
<script setup>
import Header from "@/components/Header.vue";
import Footer from "@/components/Footer.vue";
import TopButton from "@/components/TopButton.vue";
import CartBtn from "@/components/CartBtn.vue"; //導入 購物車按鈕組件
import { useCartStore } from "@/stores/cartStore"; //載入pinia
import { storeToRefs } from "pinia"; // 可以使用方法
import TokenStore from "@/utils/TokenStore"; //userId
import axios from "axios";

// 初始化 Pinia 的 store
const cartStore = useCartStore();
// 使用 storeToRefs 解構 state 和 getters
const { items, totalPrice, totalQuantity, allSelected } =
  storeToRefs(cartStore);
// 直接從 store 中使用 actions
const {
  removeItem,
  clearCart,
  updateQuantity,
  setDefaultQuantity,
  toggleAllSelection,
  toggleItemSelection,
} = cartStore;

console.log("items 值的實際資料:", items);
// 結帳
const checkOut = async () => {
  // 從 Token 中取得 userId
  const token = TokenStore.getToken();
  const payload = TokenStore.decodeToken(token); // 解析 token
  const userId = payload?.userId; // 獲取 userId
  if (!token) {
    console.error("請先登入再結帳");
    alert("請先登入再結帳");
    return;
  }
  console.log("userId:", userId);
  console.log("items:", items.value);
  //組合購物車資料
  const buyItemList = items.value.map((item) => ({
    productId: item.productId,
    quantity: item.quantity,
  }));
  //發送api
  try {
    const response = await axios.post(
      `http://localhost:8080/orders/${userId}/createOrder`,
      {
        Header:{
          Authorization: `Bearer ${token}`,
        },
        buyItemList: buyItemList,
      }
    );
    console.log("訂單建立成功", response.data);
    alert("訂單建立成功！");
    clearCart(); // 清空購物車
  } catch (error) {
    // axios 錯誤物件
    if (error.response) {
      console.error("後端返回錯誤：", error.response.data);
      alert(`錯誤：${error.response.data.message}`);
    } else {
      console.error("結帳時發生錯誤：", error.message);
    }
  }
};

// 印出 items 中的所有商品資料
console.log("items.value", items.value);

// 或者使用 forEach 來遍歷並逐一印出每個商品
items.value.forEach((item, index) => {
  console.log(`cart.vue 商品 ${index + 1}:`, item);
});
</script>

<style scoped>
/* 設定購物車容器 */
.cart-contain {
  max-width: 1000px;
  min-height: 500px;
  margin: 20px auto;
  padding: 20px;
  border: none;
  border-radius: 5px;
  background-color: #f5f5f5;
}

/* 表格樣式 */
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

thead {
  background-color: orange;
  color: #fff;
}

th,
td {
  padding: 12px;
  width: 150px;
  text-align: center;
  border: 1px solid white;
}

/* 商品圖片樣式 */
img {
  max-width: 100px;
  height: auto;
  border-radius: 4px;
}

/* 按鈕樣式 */
button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: #ff4d4f;
  color: #fff;
  cursor: pointer;
  margin: 0 10px;
  font-size: 14px;
}

button:hover {
  background-color: #ff7875;
}

button:active {
  background-color: #d9363e;
}

/* 清空按鈕樣式 */
button:last-child {
  background-color: #7b5e36;
}

button:last-child:hover {
  background-color: #a07b47;
}

/* 總金額與總數量的文字樣式 */
.total {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  margin: 30px;
  font-size: 1em;
  color: gray;
}

.allcheck,.checkinput {
  border: none;
  outline: none;
  box-shadow: none;
}

.total p {
  margin: 5px;
  font-weight: bold;
}

.bottom-btn {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  margin: 0 16px;
}

.numinput {
  width: 100px;
  text-align: center;
  border: 1px solid #ccc;
  border-radius: 5px;
  padding: 10px;
  font-size: 14px;
}
</style>
