<template>
    <div class="forgot-password">
      <h1>忘記密碼</h1>
      <p>請輸入您的電子郵件地址，我們將發送一封包含密碼重置連結的郵件給您。</p>
  
      <!-- Email 輸入表單 -->
      <form @submit.prevent="handleSubmit">
        <label for="email">電子郵件：</label>
        <input
          type="email"
          id="email"
          v-model="email"
          placeholder="請輸入您的電子郵件"
          required
        />
        <button type="submit">發送重置密碼連結</button>
      </form>
  
      <!-- 顯示訊息 -->
      <p v-if="message" class="message">{{ message }}</p>
      <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>
    </div>
  </template>
  
  <script setup>
  import { ref } from "vue";
  import api from "@/utils/Request.js";

      const email = ref("");
      const message = ref("");
      const errorMessage = ref("");
  
      // 提交表單的處理函數
      const handleSubmit = async () => {
        try {
          // 向後端發送 POST 請求
          const response = await api.post("/api/user/resetPassword", {
            email:email.value,
          });
  
          // 成功時顯示訊息
          message.value = "重置密碼連結已發送到您的電子郵件！";
          errorMessage.value = "";
        } catch (error) {
          // 失敗時顯示錯誤訊息
          errorMessage.value =
            error.response?.data?.message || "發送重置密碼連結時發生錯誤！";
          message.value = "";
        }
      };
  </script>
  
  <style scoped>
  .forgot-password {
    max-width: 400px;
    margin: 50px auto;
    padding: 20px;
    border: 1px solid #ddd;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  }
  
  h1 {
    text-align: center;
    font-size: 24px;
    margin-bottom: 16px;
  }
  
  form {
    display: flex;
    flex-direction: column;
  }
  
  label {
    margin-bottom: 8px;
    font-weight: bold;
  }
  
  input {
    margin-bottom: 16px;
    padding: 8px;
    font-size: 16px;
    border: 1px solid #ccc;
    border-radius: 4px;
  }
  
  button {
    padding: 10px 15px;
    font-size: 16px;
    color: #fff;
    background-color: #007bff;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }
  
  button:hover {
    background-color: #0056b3;
  }
  
  .message {
    color: green;
    margin-top: 16px;
  }
  
  .error-message {
    color: red;
    margin-top: 16px;
  }
  </style>
  