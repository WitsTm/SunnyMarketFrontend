<template>
  <div>
    <h1>重新設定密碼</h1>
    <form
      id="reset-password-form"
      class="reset-pwd-container"
      @submit.prevent="resetPassword"
    >
      <!-- 使用 token -->
      <input class="input-box" type="hidden" name="token" :value="token" />
      <input
        type="password"
        id="password"
        v-model="password"
        placeholder="請輸入新密碼"
        required
      />
      <button class="submit-button" type="submit">重置密碼</button>
    </form>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";
import axios from "axios"; // 假設有一個全域的 api 模組

// 接收來自路由的 token
const props = defineProps({
  token: String,
});

// 定義響應式變數
const password = ref("");

// 路由功能
const router = useRouter();

// 方法：重置密碼
const resetPassword = async () => {
  try {
    // 發送 POST 請求到後端
    await axios.post("/api/auth/reset-password", {
      token: props.token,
      password: password.value,
    });
    alert("密碼已成功重置！");
    router.push("/login"); // 重置成功後跳轉到登入頁
  } catch (error) {
    alert("重置失敗，請稍後再試！");
  }
};
</script>

<style scoped>
/* 根容器樣式 */
.reset-pwd-container {
  max-width: 400px;
  margin: 50px auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 10px;
  background-color: #f9f9f9;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

/* 輸入框樣式 */
.input-box {
  width: 100%;
  padding: 10px 15px;
  font-size: 1em;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-sizing: border-box;
  transition: border-color 0.3s;
}

.input:focus {
  border-color: #007bff;
  outline: none;
}

/* 按鈕樣式 */
.submit-button {
  width: 100%;
  padding: 10px 15px;
  margin-top: 20px;
  font-size: 1em;
  color: #fff;
  background-color: #007bff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.submit-button:hover {
  background-color: #0056b3;
}
</style>
