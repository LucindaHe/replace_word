<template>
  <div>
    <div class="btnGroup">
      <button class="tab-pri" @click="activeTab = 'tab1'">轉換和諧字</button>
      <button class="tab-pri" @click="activeTab = 'tab2'">管理資料庫</button>
    </div>

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

    <div class="tabContent" v-if="activeTab === 'tab1'">
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
          <button class="btn-pri" @click="checkText">開始轉換</button>
          <button class="btn-pri" @click="copyText">複製文字</button>
        </div>
        <div>
          <div class="scroll-container" v-if="textFound">
            <div>
              <table class="ManageData-bordered_table">
                <thead>
                  <tr>
                    <th>被和諧字</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="e in replacedWord" :key="e">
                    <td>{{ e }}</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
          <p v-else>文章中不包含關鍵字。</p>
        </div>
      </div>
    </div>
    <div class="tabContent" v-if="activeTab === 'tab2'">
      <div class="scroll-container" v-if="excelData.length != 0">
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
    </div>
  </div>
</template>

<script>
import * as XLSX from "xlsx"; // vue3可用此引入

export default {
  name: "App",
  components: {},

  data() {
    return {
      textFound: false,
      text_value: "",
      InputFile: null,
      excelData: [],
      replacedWord: [],
      key: "",
      value: "",
      fileName: "",
      activeTab: "tab1", // Initial active tab
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
        };

        reader.readAsArrayBuffer(file);
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
        this.replacedWord = [];
        this.textFound = false;

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
            this.replacedWord.push(element[0]);
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
  .tab-pri {
    @extend %btn-default;
    width: fit-content;
    height: fit-content;
    margin-right: 10px;
    background-color: transparent;
    color: #000000;
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
  margin-top: 10px;
}

.ManageData {
  &-bordered_table {
    width: 50%; /* 表格宽度 */

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
}

.tabContent {
  height: 1100px;
  margin-top: 10px;
}
</style>
