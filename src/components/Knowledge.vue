<template>
  <!-- 知识窗口组件，仅在isShowing为true时显示 -->
  <div class="knowledge-window" v-if="isShowing">
    <!-- 知识窗口的头部，包括标题和关闭按钮 -->
    <div class="board-header">
      <h1 class="board-title">碳汇基础知识</h1>
      <button class="close-button" @click="closeWindow">关闭</button>
    </div>
    <!-- 显示用户点击的知识标题 -->
    <h2>{{ clickedTitle }}</h2>
    <!-- 显示知识内容，内容来源于Basekonwledge.json文件 -->
    <div class="knowledge-content" v-html="content"></div>
  </div>
</template>

<script>
import knowledgeBase from "./Basekonwledge.json";

export default {
  // 接收从父组件传递的clickedTitle属性
  props: ["clickedTitle"],
  data() {
    return {
      // 控制知识窗口的显示和隐藏
      isShowing: false,
    };
  },
  computed: {
    content() {
      // 从Basekonwledge.json中获取与clickedTitle匹配的知识内容
      const item = knowledgeBase.knowledgeList.find(
        (knowledge) => knowledge.title === this.clickedTitle
      );
      return item ? item.content : "";
    },
  },
  methods: {
    // 关闭知识窗口
    closeWindow() {
      this.isShowing = false;
    },
    // 打开知识窗口
    openWindow() {
      this.isShowing = true;
    },
  },
};
</script>

<style scoped>
/* 知识窗口的主体样式，包括位置、宽高、背景、边框、阴影等 */
.knowledge-window {
  position: absolute;
  width: 69%;
  height: 85%;
  background: rgba(255, 255, 255, 0.9);
  top: 9%;
  left: 2%;
  z-index: 1000;
  overflow: auto;
  border-radius: 10px;
  padding: 20px;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
}

/* 知识窗口头部的布局，使标题和关闭按钮水平排列并在两端对齐 */
.board-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.board-title {
  font-size: 18px;
}

/* 关闭按钮的样式，包括背景、颜色、边框、字体、鼠标样式等 */
.close-button {
  background-color: rgba(26, 42, 29, 0.908);
  color: #fff;
  font-weight: bold;
  padding: 10px 20px;
  border: none;
  cursor: pointer;
  border-radius: 5px;
  font-size: 16px;
}
</style>
