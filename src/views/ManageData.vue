<template>
  <div class="ManageData">
    <div>
      <table class="ManageData-bordered_table">
        <thead>
          <tr>
            <th>被和諧字</th>
            <th>取代字</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="e in dataList" :key="e">
            <td>{{ e[0] }}</td>
            <td>{{ e[1] }}</td>
          </tr>
        </tbody>
      </table>

      <div class="horizontal_list">
        <input
          class="textSize"
          type="text"
          v-model="key"
          placeholder="輸入被和諧字"
        />
        <input
          class="textSize"
          type="text"
          v-model="value"
          placeholder="輸入取代字"
        />
        <div @click="addData">新增</div>
      </div>
    </div>
  </div>
</template>

<script>
import replaceWords from "@/database/replaceWord.json";

export default {
  name: "ManageData",
  components: {},

  data() {
    return {
      replaceWords: replaceWords,
      dataList: {},
      key: "",
      value: "",
    };
  },

  mounted() {
    this.arrangeList();
  },

  methods: {
    arrangeList() {
      this.dataList = Object.entries(this.replaceWords);
    },

    addData() {
      console.log("test", this.key);

      let source = {};
      source = { [this.key]: this.value };
      console.log("source", source);

      let target = {};
      target = this.dataList;
      console.log("target", target);

      this.replaceWords[this.key] = this.value;
      console.log("this.replaceWords", this.replaceWords);

      this.dataList = Object.entries(this.replaceWords);
    },
  },
};
</script>

<style lang="scss">
.ManageData {
  &-bordered_table {
    border-collapse: collapse; /* 合并边框 */
    width: 100%; /* 表格宽度 */
    border: 1px solid #ccc; /* 表格边框 */

    th,
    td {
      border: 1px solid #ccc; /* 单元格边框 */
      padding: 8px; /* 单元格内边距 */
    }
  }
  .horizontal_list {
    display: flex;
    list-style: none; /* 去除默认的列表样式 */
    padding: 0; /* 去除默认的内边距 */
    div {
      margin-right: 10px; /* 项之间的间距 */
      font-size: 20px;
      margin-top: 10px;
    }
  }

  .textSize {
    font-size: 20px;
    margin-top: 10px;
    margin-right: 10px;
  }
}
</style>
