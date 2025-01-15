<template>
    <div>
        <h1>重置密碼</h1>
        <form id="reset-password-form" @submit.prevent="resetPassword">
            <!-- 使用 token -->
            <input type="hidden" name="token" :value="token" />
            <label for="password">新密碼：</label>
            <input type="password" id="password" v-model="password" required />
            <button type="submit">重置密碼</button>
        </form>
    </div>
</template>

<script>

export default {
    props: ['token'], // 接收來自路由的 token
    data() {
        return {
            password: '', // 綁定輸入的新密碼
        };
    },
    methods: {
        async resetPassword() {
            try {
                // 發送 POST 請求到後端
                await api.post('/api/auth/reset-password', {
                    token: this.token,
                    password: this.password,
                });
                alert('密碼已成功重置！');
                this.$router.push('/login'); // 重置成功後跳轉到登入頁
            } catch (error) {
                alert('重置失敗，請稍後再試！');
            }
        },
    },
};
</script>

<style scoped>
/* 根據需求進行樣式設計 */
</style>