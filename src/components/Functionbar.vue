<template>
  <!-- 功能条组件 -->
  <div class="functionbar">
    <!-- 循环渲染功能块 -->
    <div class="function-blocks">
      <div v-for="(functionBlock, index) in functionBlocks" :key="index">
        <!-- 功能块标题，点击后可以展开或折叠相应的内容 -->
        <div class="function-block" @click="toggleBlock(index)">
          <div class="function-block-title">
            {{ functionBlock.title }}
          </div>
        </div>
        <!-- 功能块内容的展开与折叠动画效果 -->
        <transition name="fade">
          <!-- 当选中的功能块展开时，显示其内容 -->
          <div
            v-if="currentBlockIndex === index && functionBlock.expanded"
            class="function-content"
          >
            <!-- 这里根据功能块的索引来显示不同的功能内容 -->
            <div v-if="index === 0">
              <!-- 用户信息管理部分 -->
              <div class="sub-section">
                <div class="user-management">
                  <h3>数据库连接</h3>
                  <div class="input-container">
                    <label>用户名:</label>
                    <input type="text" v-model="username" />
                  </div>
                  <div class="input-container">
                    <label>密码:</label>
                    <input type="password" v-model="password" />
                  </div>
                  <div class="submit-container">
                    <button @click="loginUser">连接数据库</button>
                    <transition name="fade">
                      <span v-show="loginStatus">{{ loginMessage }}</span>
                    </transition>
                  </div>
                </div>
              </div>

              <div class="sub-section">
                <div class="user-registration">
                  <h3>用户注册</h3>
                  <div class="input-container">
                    <label>用户名:</label>
                    <input type="text" v-model="regUsername" />
                  </div>
                  <div class="input-container">
                    <label>设置密码:</label>
                    <input type="password" v-model="regPassword" />
                  </div>
                  <div class="input-container">
                    <label>确认密码:</label>
                    <input type="password" v-model="confirmPassword" />
                  </div>
                  <div class="input-container">
                    <label>邮箱:</label>
                    <input type="email" v-model="email" />
                  </div>
                  <div class="input-container">
                    <label>姓名:</label>
                    <input type="text" v-model="realName" />
                  </div>
                  <div class="input-container">
                    <label>身份证:</label>
                    <input type="text" v-model="idCard" />
                  </div>
                  <div class="submit-container">
                    <button class="wider-button" @click="submitRegistration">
                      提交
                    </button>
                    <transition name="fade">
                      <span v-show="registrationMessage">{{
                        registrationMessage
                      }}</span>
                    </transition>
                  </div>
                </div>
              </div>
            </div>
            <div v-if="index === 1">
              <!-- 碳汇计算功能部分 -->
              <div class="sub-section">
                <div class="row">
                  <div class="dropdown-container">
                    <div class="select-group">
                      <label>地区:</label>
                      <select v-model="selectedRegion">
                        <option disabled value=""></option>
                        <option>福州市</option>
                        <option>厦门市</option>
                        <option>莆田市</option>
                        <option>三明市</option>
                        <option>泉州市</option>
                        <option>漳州市</option>
                        <option>南平市</option>
                        <option>龙岩市</option>
                        <option>宁德市</option>
                      </select>
                    </div>
                    <div class="select-group">
                      <button @click="calculateCarbonSequestration">
                        计算
                      </button>
                    </div>
                  </div>
                  <div class="dropdown-container">
                    <div class="select-group">
                      <label>林班:</label>
                      <select v-model="selectedLinBan">
                        <option disabled value=""></option>
                        <option v-for="number in 100" :key="number">
                          {{ number }}
                        </option>
                      </select>
                    </div>
                    <div class="select-group">
                      <label>小班:</label>
                      <select v-model="selectedXiaoBan">
                        <option disabled value=""></option>
                        <option v-for="number in 100" :key="number">
                          {{ number }}
                        </option>
                      </select>
                    </div>
                  </div>
                </div>
                <div class="divider"></div>
                <div class="row">
                  <div class="checkbox-container">
                    <label>
                      <input
                        type="radio"
                        v-model="selectedOption"
                        value="整体环境碳储量"
                      />
                      整体环境碳储量
                    </label>

                    <label>
                      <input
                        type="radio"
                        v-model="selectedOption"
                        value="乔木碳储量"
                      />
                      乔木碳储量
                    </label>
                  </div>
                </div>
                <div class="row">
                  <div class="checkbox-container">
                    <label>
                      <input
                        type="radio"
                        v-model="selectedOption"
                        value="灌木碳储量"
                      />
                      灌木碳储量
                    </label>
                    <label>
                      <input
                        type="radio"
                        v-model="selectedOption"
                        value="草本碳储量"
                      />
                      草本碳储量
                    </label>
                  </div>
                </div>
                <div class="divider"></div>
                <div class="row">
                  <div class="carbon-sequestration-display">
                    <span class="carbon-label">固碳量:</span>
                    <span class="carbon-sequestration-value">{{
                      carbonSequestration
                    }}</span>
                  </div>
                </div>
                <div class="divider"></div>
                <div class="table-container">
                  <h3>小班因子(基本信息)</h3>
                  <table>
                    <tr>
                      <th>优势树种</th>
                      <th>小班蓄积量</th>
                      <th>小班面积</th>
                      <th>龄级</th>
                      <th>龄组</th>
                    </tr>
                    <tr>
                      <td>{{ data.superiorTreeSpecies }}</td>
                      <td>{{ data.subcompartmentVolume }}</td>
                      <td>{{ data.subcompartmentArea }}</td>
                      <td>{{ data.ageLevel }}</td>
                      <td>{{ data.ageGroup }}</td>
                    </tr>
                    <tr>
                      <th>公项株数</th>
                      <th>地类</th>
                      <th>疏密度</th>
                      <th>高度</th>
                    </tr>
                    <tr>
                      <td>{{ data.publicNumberOfStems }}</td>
                      <td>{{ data.landClass }}</td>
                      <td>{{ data.sparseness }}</td>
                      <td>{{ data.height }}</td>
                    </tr>
                  </table>
                </div>
              </div>
            </div>
            <div v-if="index === 2">
              <!-- 地区概况部分 -->
              <div class="sub-section">
                <div class="region-overview">
                  <div class="dropdown-container">
                    <label>地区:</label>
                    <select v-model="selectedRegion" @change="fetchRegionData">
                      <option disabled value=""></option>
                      <option>福州市</option>
                      <option>厦门市</option>
                      <option>莆田市</option>
                      <option>三明市</option>
                      <option>泉州市</option>
                      <option>漳州市</option>
                      <option>南平市</option>
                      <option>龙岩市</option>
                      <option>宁德市</option>
                    </select>
                  </div>
                  <div class="region-data" v-html="regionData"></div>
                </div>
              </div>
            </div>
            <div v-if="index === 3" class="sub-section">
              <div class="section-container">
                <div class="row rowone">
                  <div class="select-group">
                    <label>林分类型:</label>
                    <select v-model="selectedForestType">
                      <option disabled value="">无</option>
                      <option>白桦</option>
                      <option>树</option>
                      <option>红松</option>
                      <option>山杨</option>
                      <option>杂木</option>
                      <option>作树</option>
                      <option>落叶松</option>
                      <option>人工红松</option>
                      <option>云冷杉林</option>
                      <option>人工落叶松</option>
                      <option>针阔混交林</option>
                      <option>阔叶混交林</option>
                      <option>珍贵硬阔叶混交林</option>
                    </select>
                  </div>
                  <div class="select-group">
                    <label>龄组:</label>
                    <select v-model="selectedAgeGroup">
                      <option disabled value="">无</option>
                      <option>成</option>
                      <option>过</option>
                      <option>近</option>
                      <option>幼</option>
                      <option>中</option>
                    </select>
                  </div>
                </div>
                <div class="divider"></div>
                <div class="row">
                  <h3>林分信息</h3>
                  <div class="range-container">
                    <div class="input-group">
                      <label class="label-margin">公顷株数:</label>
                      <input
                        type="number"
                        v-model="minStems"
                        min="170"
                        max="8899"
                        placeholder="170"
                      />
                      <span class="text-gap">至</span>
                      <input
                        type="number"
                        v-model="maxStems"
                        min="170"
                        max="8899"
                        placeholder="8899"
                      />
                    </div>
                    <p class="range-reminder">(范围为170-8899)</p>
                  </div>
                  <div class="select-group">
                    <label>林分高:</label>
                    <select
                      v-model="selectedMinForestHeight"
                      class="select-gap"
                    >
                      <option disabled value="">选择下限</option>
                      <option v-for="number in 35" :key="number">
                        {{ number }}
                      </option>
                    </select>
                    <select
                      v-model="selectedMaxForestHeight"
                      class="select-gap"
                    >
                      <option disabled value="">选择上限</option>
                      <option v-for="number in 35" :key="number">
                        {{ number }}
                      </option>
                    </select>
                  </div>
                </div>
                <div class="divider"></div>

                <div class="row">
                  <h3>地形地貌</h3>
                  <div>
                    <label>地貌:</label>
                    <label
                      v-for="(terrain, index) in terrainOptions"
                      :key="index"
                      class="select-gap"
                    >
                      <input
                        type="checkbox"
                        :value="terrain"
                        v-model="selectedTerrain"
                      />
                      {{ terrain }}
                    </label>
                  </div>
                  <div class="select-group">
                    <label>坡度:</label>
                    <select v-model="selectedSlope" class="select-gap">
                      <option disabled value="">选择</option>
                      <option v-for="number in 35" :key="number">
                        {{ number }}
                      </option>
                    </select>
                  </div>
                  <div class="select-group">
                    <label>坡向:</label>
                    <select v-model="selectedAspect" class="select-gap">
                      <option disabled value="">选择</option>
                      <option>东</option>
                      <option>南</option>
                      <option>西</option>
                      <option>北</option>
                      <option>西南</option>
                      <option>西北</option>
                      <option>东南</option>
                      <option>东北</option>
                    </select>
                  </div>
                  <div class="select-group">
                    <label>坡位:</label>
                    <select v-model="selectedPosition" class="select-gap">
                      <option disabled value="">选择</option>
                      <option>谷</option>
                      <option>脊</option>
                      <option>平地</option>
                      <option>上</option>
                      <option>下</option>
                      <option>中</option>
                    </select>
                  </div>
                </div>
                <div class="divider"></div>
                <div class="row">
                  <button class="wider-button" @click="submitQuery">
                    查询
                  </button>
                </div>
              </div>
            </div>
            <div v-if="index === 4" class="sub-section">
              <!-- 图层控制部分 -->
              <div class="layer-control-part">
                <div class="layer-control-row title-row">
                  <span>请选择需要的底图</span>
                </div>
                <div class="layer-control-row option-row">
                  <select v-model="selectedBaseMap" :disabled="lockLayer">
                    <option value="航空图">航空图</option>
                    <option value="行政区划图">行政区划图</option>
                  </select>
                  <input type="checkbox" v-model="lockLayer" /> 锁定图层
                </div>
              </div>
              <div class="divider"></div>
              <!-- 第二个部分 -->
              <div class="layer-control-part">
                <div class="layer-control-row title-row">
                  地区：
                  <select v-model="selectedRegion">
                    <option>福州市</option>
                    <option>厦门市</option>
                    <option>莆田市</option>
                    <option>三明市</option>
                    <option>泉州市</option>
                    <option>漳州市</option>
                    <option>南平市</option>
                    <option>龙岩市</option>
                    <option>宁德市</option>
                  </select>
                </div>
                <div class="layer-control-row title-row">选择显示的图层</div>
                <div class="layer-control-row checkbox-row">
                  <label>
                    <input type="checkbox" v-model="showCarbonStorage" />
                    碳储量
                  </label>
                  <label>
                    <input type="checkbox" v-model="showNDVI" /> NDVI
                  </label>
                  <label>
                    <input type="checkbox" v-model="showCO2Concentration" />
                    二氧化碳浓度
                  </label>
                  <label>
                    <input
                      type="checkbox"
                      v-model="showVegetationClassification"
                    />
                    植被分类
                  </label>
                </div>
              </div>
            </div>
            <div v-if="index === 5" class="sub-section">
              <!-- 碳汇相关部分 -->
              <div class="carbon-related-section">
                <!-- 碳汇基础知识 -->
                <div class="carbon-knowledge-section">
                  <h3>碳汇基础知识</h3>
                  <ul>
                    <li
                      @click="
                        openKnowledgeWindow('《联合国气候变化框架公约》简介')
                      "
                    >
                      《联合国气候变化框架公约》简介
                    </li>
                    <li
                      @click="
                        openKnowledgeWindow('《中国应对气候变化国家方案》简介')
                      "
                    >
                      《中国应对气候变化国家方案》简介
                    </li>
                    <li
                      @click="
                        openKnowledgeWindow('中国应对气候变化面临的七大挑战')
                      "
                    >
                      中国应对气候变化面临的七大挑战
                    </li>
                    <li
                      @click="
                        openKnowledgeWindow(
                          '《中国应对气候变化国家方案》中与林业相关的政策和措施'
                        )
                      "
                    >
                      《中国应对气候变化国家方案》中与林业相关的政策和措施
                    </li>
                    <li
                      @click="
                        openKnowledgeWindow(
                          '我国《应对气候变化林业行动计划》主要内容'
                        )
                      "
                    >
                      我国《应对气候变化林业行动计划》主要内容
                    </li>
                    <li
                      @click="openKnowledgeWindow('中国林业减缓气候变化的途径')"
                    >
                      中国林业减缓气候变化的途径
                    </li>
                  </ul>
                </div>

                <!-- 碳汇新闻 -->
                <div class="carbon-news-section">
                  <h3>碳汇新闻</h3>
                </div>
              </div>
            </div>
          </div>
        </transition>
      </div>
    </div>
    <Knowledge
      ref="knowledgeWindow"
      :clickedTitle="clickedTitle"
      :content="content"
    />
  </div>
</template>

<script>
import Knowledge from "./Knowledge.vue";

export default {
  // props: ['current-user'],
  components: { Knowledge },
  data() {
    return {
      // 用户信息管理相关的数据
      curruser: "",
      username: "",
      password: "",
      regUsername: "",
      regPassword: "",
      confirmPassword: "",
      email: "",
      realName: "",
      idCard: "",
      connectionSuccess: false,
      registrationSuccess: false,
      loginMessage: "", // 用于显示登录的提示信息
      loginStatus: false, // 用于记录用户是否已经登录
      registrationMessage: "", //用于显示注册的提示信息
      // 模拟登录信息
      mockUsers: {
        alice: "123456", // 用户名: 密码
        bob: "password",
        charlie: "qwerty",
      },
      // 功能块的状态和内容
      currentBlockIndex: null,
      functionBlocks: [
        { title: "用户信息管理", expanded: false },
        { title: "碳汇计算功能", expanded: false },
        { title: "地区概况", expanded: false },
        { title: "多条件查询", expanded: false },
        { title: "图层控制", expanded: false },
        { title: "碳汇相关", expanded: false },
      ],
      selectedRegion: "",
      selectedLinBan: "",
      selectedXiaoBan: "",
      selectedOption: "",
      carbonSequestration: "",
      data: {
        superiorTreeSpecies: "",
        subcompartmentVolume: "",
        subcompartmentArea: "",
        ageLevel: "",
        ageGroup: "",
        publicNumberOfStems: "",
        landClass: "",
        sparseness: "",
        height: "",
      },
      regionData: "",
      // 多条件查询
      selectedForestType: "",
      selectedAgeGroup: "",
      minStems: 170,
      maxStems: 8899,
      selectedMinForestHeight: "",
      selectedMaxForestHeight: "",
      densityOptions: [0, 0.1, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0],
      selectedDensity: [],
      terrainOptions: ["低山", "平地"],
      selectedTerrain: [],
      selectedSlope: "",
      selectedAspect: "",
      selectedPosition: "",

      // 碳汇基础知识
      clickedTitle: "", // 点击的标题
      content: "", // 对应的内容

      // 图层控制
      lockLayer: false, // 用于锁定图层的标识
      selectedBaseMap: "", // 用于保存选中的底图类型
    };
  },
  methods: {

    toggleBlock(index) {
      if (this.currentBlockIndex !== null) {
        this.functionBlocks[this.currentBlockIndex].expanded = false;
      }
      if (this.currentBlockIndex !== index) {
        this.currentBlockIndex = index;
        setTimeout(() => {
          this.functionBlocks[this.currentBlockIndex].expanded = true;
        }, 500);
      } else {
        this.currentBlockIndex = null;
      }
    },
    // 用户登录
    loginUser() {
      // 检查用户名和密码是否为空
      if (!this.username.trim()) {
        this.loginStatus = true;
        this.loginMessage = "用户名不能为空！";
        return;
      }
      if (!this.password.trim()) {
        this.loginStatus = true;
        this.loginMessage = "密码不能为空！";
        return;
      }

      // 检查用户名和密码是否匹配
      if (this.mockUsers[this.username]) {
        if (this.mockUsers[this.username] === this.password) {
          this.loginStatus = true;
          this.loginMessage = "数据库已连接！";
          setTimeout(() => {
            this.loginMessage = ""; // 0.5秒后隐藏提示
          }, 500);
          this.curruser = this.username;
          // 发出登录成功的事件，将用户名发送到父组件
          this.$emit("user-logged-in", this.username);
        } else {
          this.loginStatus = true;
          this.loginMessage = "密码不正确！";
        }
      } else {
        this.loginStatus = true;
        this.loginMessage = "用户名不存在！";
      }
    },
    // 用户注册
    submitRegistration() {
      this.sendData();
      // 检查所有的输入框是否都已填写
      if (!this.regUsername.trim()) {
        this.registrationMessage = "用户名不能为空！";
        return;
      }
      if (!this.regPassword.trim()) {
        this.registrationMessage = "密码不能为空！";
        return;
      }
      if (!this.confirmPassword.trim()) {
        this.registrationMessage = "请确认密码！";
        return;
      }
      if (!this.email.trim()) {
        this.registrationMessage = "邮箱不能为空！";
        return;
      }
      if (!this.realName.trim()) {
        this.registrationMessage = "姓名不能为空！";
        return;
      }
      if (!this.idCard.trim()) {
        this.registrationMessage = "身份证不能为空！";
        return;
      }

      // 检查设置的密码和确认密码是否相同
      if (this.regPassword !== this.confirmPassword) {
        this.registrationMessage = "设置的密码和确认密码不一致！";
        return;
      }

      // 检查用户名是否已经存在
      if (this.mockUsers[this.regUsername]) {
        this.registrationMessage = "用户名已存在！";
        return;
      }

      // 如果上述验证都通过，那么注册成功
      this.registrationMessage = "提交成功！";
      setTimeout(() => {
        this.registrationMessage = ""; // 0.5秒后隐藏提示
      }, 500);

      // 将新用户添加到模拟用户数据中
      this.mockUsers[this.regUsername] = this.regPassword;
    },
    calculateCarbonSequestration() {
      // 在这里实现碳汇计算的逻辑
      // 假设计算结果为1000
      this.carbonSequestration = 1000;
      // 假设后端返回的数据如下
      this.data = {
        superiorTreeSpecies: "杉木",
        subcompartmentVolume: 2000,
        subcompartmentArea: 3000,
        ageLevel: 20,
        ageGroup: "20-30",
        publicNumberOfStems: 4000,
        landClass: "乔木林",
        sparseness: 0.6,
        closure: 0.8,
        height: 25,
      };
    },
    fetchRegionData() {
      // 在这里实现从后端获取地区简介的逻辑
      // 假设后端返回的数据如下
      this.regionData = `
        <h2>${this.selectedRegion}</h2>
        <img src="https://example.com/image.jpg" alt="${this.selectedRegion}" />
        <p>这是${this.selectedRegion}的简介。</p>
      `;
    },
    fetchData() {
      // 在这里实现查询逻辑，将用户的选项作为参数发送给后端
      // 假设查询成功，更新数据
      this.data = {
        //
      };
    },

    //多条件查询
    async submitQuery() {
      const results = []; // 这里可以添加一些模拟数据或保持为空数组
      this.$emit("queryResults", results);
    },
    async getQueryResultsFromBackend() {
      // 在这里实现查询逻辑，并返回结果
      // 示例：
      // return await axios.post('/api/query', { /* 查询参数 */ });
      // 碳汇基础知识
    },

    // 碳汇基础知识

    openKnowledgeWindow(title) {
      // 设置点击的标题和对应的内容
      this.clickedTitle = title;
      this.content = "对应的内容"; // 这里可以根据需要设置具体内容

      // 打开Knowledge组件的浮窗
      this.$refs.knowledgeWindow.openWindow();
    },
  },
};
</script>

<style scoped>
.functionbar {
  display: flex;
  flex-direction: column;
  height: 100%;
  overflow: auto;
}

.function-blocks {
  display: flex;
  flex-direction: column;
}

.function-block {
  display: flex;
  flex-direction: column;
  border: 1.5px solid #ccc;
  background-color: rgba(26, 42, 29, 0.908);
  padding: 10px;
}

.function-block-title {
  cursor: pointer;
  text-align: center;
  color: white;
  font-size: 17px;
}

.function-content {
  padding: 10px;
  border-radius: 10px;
  background: #f0f0f0;
  margin: 10px 0;
}

.sub-section {
  background: #f0f0f0de;
  border: 1px solid #878686;
  border-radius: 10px;
  padding: 10px;
  margin-bottom: 5px;
}

.sub-section h3 {
  color: #333;
  margin-bottom: 10px;
}

.input-container {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  margin-bottom: 10px;
  padding: 0 15px;
}

.submit-container {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
}

button {
  padding: 5px 10px;
  font-size: 16px;
  border-radius: 5px;
  background-color: #1d2b1f;
  color: white;
  border: none;
  cursor: pointer;
}

.wider-button {
  width: 100px;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.01s;
}

.fade-enter,
.fade-leave-to {
  transition: opacity 0.01s;
}

.row {
  margin-bottom: 30px;
}

.dropdown-container {
  display: flex;
  justify-content: space-between;
  margin-bottom: 15px;
}

.select-group {
  margin-right: 30px;
}

.select-group label {
  margin-right: 10px;
}

.checkbox-container {
  display: flex;
  justify-content: space-around;
}

.carbon-sequestration-display {
  display: flex;
  align-items: center;
}

.carbon-label {
  margin-right: 10px;
}

.carbon-sequestration-value {
  border: 2px solid #827e7e;
  padding: 5px;
  width: 200px;
  display: inline-block;
  text-align: center;
}

.table-container table {
  width: 100%;
  text-align: center;
}

.table-container th,
.table-container td {
  border: 1px solid #ddd;
  padding: 8px;
}

.divider {
  border-top: 1px solid #6e6d6d;
  margin: 15px 0;
}

.range-reminder {
  font-size: 12px;
  color: #706e6e;
}

.label-margin {
  margin-right: 20px;
}

.select-gap {
  margin-left: 25px;
}

.rowone {
  display: flex;
  flex-direction: row;
}

.text-gap {
  margin: 0 20px;
}

.layer-control-part {
  margin-bottom: 15px;
}

.layer-control-row.title-row {
  margin-bottom: 15px;
}

.layer-control-row.option-row {
  margin-bottom: 15px;
}

.layer-control-row.checkbox-row {
  display: flex;
  justify-content: space-between;
}

/* 碳汇相关 */
.carbon-related-section {
  margin: 15px 0;
}

.carbon-knowledge-section,
.carbon-news-section {
  margin-bottom: 20px;
}

.carbon-knowledge-section h3,
.carbon-news-section h3 {
  margin-bottom: 10px;
}

/* 碳汇基础知识 */
.carbon-knowledge-section ul {
  list-style-type: none; /* 去掉文字前面的点 */
  padding-left: 0; /* 移除左侧填充，确保列表项与容器左边对齐 */
}

.carbon-knowledge-section li {
  cursor: pointer; /* 添加鼠标指针，使列表项看起来是可点击的 */
  padding: 10px 0; /* 在列表项之间添加一定的间隔 */
  border-bottom: 1px solid #ddd; /* 如果需要，可以添加底部边框 */
}

.carbon-knowledge-section li:last-child {
  border-bottom: none; /* 去掉最后一项的底部边框 */
}
</style>
