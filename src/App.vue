app.vue：
<template>
  <div id="app">
    <Navbar :username="currentUser" @user-logged-out="handleUserLoggedOut" />
    <div class="content">
      <Cesium class="Cesium" @APPVUE-user-logged-in="handleUserLoggedIn" />
      <!-- <Functionbar
        class="Functionbar"
        :current-user="currentUser"
        @user-logged-in="handleUserLoggedIn"
      /> -->
      <Board
        :show="showBoard"
        :results="queryResults"
        @close="showBoard = false"
      />
    </div>
  </div>
</template>

<script>
import Cesium from "./components/Cesium.vue";
import Navbar from "./components/Navbar.vue";
// import Functionbar from "./components/Functionbar.vue";
import Board from "./components/board.vue";

export default {
  name: "index",
  components: {
    Cesium,
    Navbar,
    // Functionbar,
    Board,
  },
  data: function () {
    return {
      // 存储浏览器窗口的高度
      fullHeight: document.documentElement.clientHeight,
      //-------------用户登录
      currentUser: null, // 添加一个属性来存储当前登录的用户名
      //-------------多条件查询结果
      // 控制 Board 组件是否显示的数据属性
      showBoard: false,
      // 存储查询结果的数据属性
      queryResults: [],
    };
  },
  methods: {
    get_BodyHeight() {
      //动态获取浏览器的高度
      const that = this;
      window.onresize = () => {
        return () => {
          window.fullHeight = document.documentElement.clientHeight;
          that.fullHeight = window.fullHeight;
        };
      };
    },

    //-------------用户登录
    handleUserLoggedIn(username) {
      this.currentUser = username;
    },
    handleUserLoggedOut() {
      this.currentUser = null;
    },

    //-------------多条件查询结果
    // 处理多条件查询结果的方法
    handleQueryResults(results) {
      // 将查询结果赋值给 queryResults 数据属性
      this.queryResults = results;
      // 设置 showBoard 为 true，以显示 Board 组件
      this.showBoard = true;
    },
  },
};
</script>

<style scoped>
/* 重置 body 的默认样式 */
body {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* 设置根组件的高度和背景色 */
#app {
  height: 100vh;
  background-color: rgba(20, 28, 20, 0.868);
}

/* 内容区域的布局和高度设置 */
.content {
  display: flex;
  height: calc(100vh - 70px);
}

/* 设置 Cesium 组件的宽度和高度 */
.Cesium {
  width: 70%;
  height: 100%;
}

/* 设置 Functionbar 组件的宽度和高度 */
.Functionbar {
  width: 35%;
  height: 100%;
}
</style>
