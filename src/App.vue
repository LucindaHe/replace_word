<template>
  <div>
    <TabView style="height: 1100px">
      <TabPanel header="轉換和諧字">
        <div>
          <div>
            <textarea
              ref="textArea"
              class="no-resize"
              style="font-size: 20px"
              v-model="text_value"
              rows="40"
              cols="150"
              autoResize
            />
          </div>
          <div class="btnGroup">
            <div class="btn-pri" @click="checkText">開始轉換</div>
            <div class="btn-pri" @click="copyText">複製文字</div>
          </div>
          <div>
            <p v-if="textFound">文章中包含關鍵字</p>
            <p v-else>文章中不包含關鍵字。</p>
          </div>
        </div>
      </TabPanel>
      <TabPanel header="管理資料庫">
        <div class="ManageData scroll-container">
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
          </div>
        </div>
      </TabPanel>
    </TabView>

    <div>
      <input
        @dragover.prevent
        @dragenter.prevent
        @drop="handleDrop"
        class="drop-area"
        type="file"
        ref="fileInput"
        @change="handleFileUpload"
      />
    </div>
  </div>
</template>

<script>
import * as XLSX from "xlsx"; // vue3可用此引入
import TabView from "primevue/tabview";
import TabPanel from "primevue/tabpanel";

export default {
  name: "App",
  components: { TabView, TabPanel },

  data() {
    return {
      textFound: false,
      text_value: "",
      InputFile: null,
      excelData: [],
      key: "",
      value: "",
      fileName: "",
    };
  },

  mounted() {},

  methods: {
    handleDrop(event) {
      event.preventDefault();
      const files = event.dataTransfer.files;
      if (files.length > 0) {
        this.$refs.fileInput.files = files;
        this.handleFile(files[0]);
        this.fileName = files[0].name;
      }
    },

    handleFileUpload() {
      const files = this.$refs.fileInput.files;
      if (files.length > 0) {
        this.handleFile(files[0]);
        this.fileName = files[0].name;
      }
    },

    handleFile(file) {
      this.InputFile = file;
      console.log("File selected:", file);
      // Perform file handling logic here

      this.ExcelToJson();
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

    copyText() {
      // 获取 textarea 元素的引用
      const textArea = this.$refs.textArea;

      // 选择文本
      textArea.select();

      try {
        // 复制选定的文本到剪贴板
        document.execCommand("copy");
        alert("文字已經複製到剪貼本！");
      } catch (err) {
        alert("無法複製");
      }
    },

    checkText() {
      if (this.InputFile != null) {
        this.ExcelToJson();

        let articleText_new = this.$refs.textArea.value;
        this.text_value = this.$refs.textArea.value;

        this.excelData.forEach((element) => {
          if (this.text_value.includes(element[0])) {
            articleText_new = this.text_value.replace(
              new RegExp(element[0], "g"),
              element[1]
            );
            this.text_value = articleText_new;
            this.textFound = true;
          }
        });
      }
    },
  },
};
</script>

<style lang="scss">
%btn-default {
  width: 100%;
  height: 40px;
  border-radius: 8px;
  box-sizing: border-box;
  font-size: 18px;
  line-height: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #6966ff;
  color: #ffffff;
  cursor: pointer;
  user-select: none;
  &:hover {
    background-color: #66b3ff;
    color: #ffffff;
  }
  &:active {
    background-color: #66b3ff;
    color: #ffffff;
  }
  &.disable {
    background-color: #66b3ff;
    pointer-events: none;
  }
}

.btnGroup {
  display: flex;
  list-style: none; /* 去除默认的列表样式 */
  padding: 0; /* 去除默认的内边距 */
  .textBtn-pri,
  .btn-pri {
    @extend %btn-default;
    width: 90px;
    height: 30px;
    margin-right: 10px;
  }
}

.no-resize {
  resize: none;
}

.drop-area {
  width: 300px;
  height: 100;
  border: 2px dashed #ccc;
  text-align: center;
  padding: 20px;
}

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
}

.scroll-container {
  max-height: 1000px; /* 设置最大高度 */
  overflow-y: auto; /* 控制垂直滚动条 */
  border: 1px solid #ccc; /* 可选：添加边框 */
}
</style>
