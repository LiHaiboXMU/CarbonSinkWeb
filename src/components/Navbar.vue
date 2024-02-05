<template>
  <nav class="navbar">
    <div class="title">福建省碳汇林业信息服务平台</div>
    <div class="user-info" v-if="username">
      <span>{{ username }}</span>
      <button @click="logout">注销</button>
    </div>
  </nav>
</template>

<script>
export default {
  name: "navbar",
  props: {
    username: String,
  },
  methods: {
    logout() {
      this.$emit("user-logged-out");
    },
  },
};
</script>

<style scoped>
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: url("../assets/bg1.png");
  /* background-color: rgba(255, 255, 255, 0.1); */
  height: 60px;
  padding: 0 20px;
  color: #fff;
}

.title {
  font-size: 20px;
  font-weight: bold;
}

.user-info {
  display: flex;
  align-items: center;
}

.user-info span {
  margin-right: 20px;
}

.user-info button {
  padding: 10px 20px;
  font-size: 14px;
  color: white;
  background-color: rgba(19, 20, 20, 0.9);
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.user-info button:hover {
  background-color: rgba(249, 249, 249, 0.3);
}
</style>
