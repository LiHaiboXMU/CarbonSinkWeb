<template>
  <div v-if="show" class="board">
    <div class="board-header">
      <span class="board-title">查询结果：{{ results.length }}条数据</span>
      <button class="close-button" @click="closeBoard">关闭</button>
    </div>
    <div class="table-container">
      <table>
        <thead>
          <tr>
            <th>代势树种</th>
            <th>公项株数</th>
            <th>坡位</th>
            <th>坡向</th>
            <th>坡度</th>
            <th>小班</th>
            <th>小班面积</th>
            <th>林分高</th>
            <th>林班</th>
            <th>龄组</th>
          </tr>
        </thead>
        <tbody>
          <tr v-if="results.length === 0" class="empty-row">
            <td colspan="10"></td>
            <!-- 占据所有列 -->
          </tr>
          <tr v-for="result in results" :key="result.id">
            <!-- 在这里插入数据 -->
            <td>{{ result.superiorTreeSpecies }}</td>
            <td>{{ result.publicNumberOfStems }}</td>
            <td>{{ result.position }}</td>
            <td>{{ result.aspect }}</td>
            <td>{{ result.slope }}</td>
            <td>{{ result.xiaoBan }}</td>
            <td>{{ result.area }}</td>
            <td>{{ result.height }}</td>
            <td>{{ result.linBan }}</td>
            <td>{{ result.ageGroup }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    show: Boolean,
    results: Array,
  },
  methods: {
    closeBoard() {
      this.$emit("close");
    },
  },
};
</script>

<style scoped>
.board {
  position: absolute;
  width: 69%;
  height: 85%;
  background: rgba(255, 255, 255, 0.9);
  top: 9%; /* 从顶部偏移5% */
  left: 2%; /* 从左边偏移5% */
  z-index: 1000;
  overflow: auto;
  border-radius: 10px; /* 添加圆角 */
  padding: 20px; /* 添加内边距 */
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1); /* 添加阴影 */
}

.board-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.board-title {
  font-size: 18px;
}

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

.table-container {
  margin: 10px; /* 表格与容器的间隔 */
}

table {
  width: 100%;
  border-collapse: collapse;
}

th,
td {
  border: 1px solid #000;
  padding: 8px;
  text-align: center;
}

tr.empty-row td {
  height: 20px;
}
</style>
