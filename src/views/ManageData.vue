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
          <tr v-for="e in excelData" :key="e">
            <td>{{ e[0] }}</td>
            <td>{{ e[1] }}</td>
          </tr>
        </tbody>
      </table>

      <div @click="ExcelToJson">開始轉換</div>

      <div>
        <div
          class="drop-area"
          @dragover.prevent
          @dragenter.prevent
          @drop="handleDrop"
        >
          Drop files here or click to select
        </div>
        <input type="file" ref="fileInput" @change="handleFileUpload" />
      </div>
    </div>
  </div>
</template>

<script>
import replaceWords from "@/database/replaceWord.json";
import * as XLSX from "xlsx"; // vue3可用此引入

export default {
  name: "ManageData",
  components: {},

  data() {
    return {
      replaceWords: replaceWords,
      dataList: {},
      key: "",
      value: "",
      excelData: [],
    };
  },

  mounted() {
    this.arrangeList();
  },

  methods: {
    handleDrop(event) {
      event.preventDefault();
      const files = event.dataTransfer.files;
      if (files.length > 0) {
        this.$refs.fileInput.files = files;
        this.handleFile(files[0]);
      }
    },
    handleFileUpload() {
      const files = this.$refs.fileInput.files;
      if (files.length > 0) {
        this.handleFile(files[0]);
      }
    },
    handleFile(file) {
      this.InputFile = file;
      console.log("File selected:", file);
      // Perform file handling logic here
    },

    ExcelToJson() {
      if (this.InputFile != null) {
        const file = this.InputFile;
        const reader = new FileReader();

        reader.onload = (e) => {
          const data = new Uint8Array(e.target.result);
          const workbook = XLSX.read(data, { type: "array" });
          const sheetName = workbook.SheetNames[0];
          const worksheet = workbook.Sheets[sheetName];
          this.excelData = XLSX.utils.sheet_to_json(worksheet, { header: 1 });
          console.log("sheetName", sheetName);
          console.log("worksheet", worksheet);
        };

        reader.readAsArrayBuffer(file);
        console.log("ExcelToJson excelData", this.excelData);
      }
    },

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
