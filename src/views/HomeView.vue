<template>
  <div class="HomeView">
    <div>
      <textarea
        ref="textArea"
        class="no-resize"
        style="font-size: 20px"
        v-model="text_value"
        rows="30"
        cols="150"
        autoResize
      />
    </div>
    <div class="horizontal_list">
      <div @click="checkText">開始轉換</div>
      <div @click="copyText">複製文字</div>
    </div>
    <div>
      <p v-if="textFound">文章中包含關鍵字</p>
      <p v-else>文章中不包含關鍵字。</p>
    </div>

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
    <router-view></router-view>
  </div>
</template>

<script>
import replaceWords from "@/database/replaceWord.json";
import * as XLSX from "xlsx"; // vue3可用此引入

export default {
  name: "HomeView",
  components: {},

  data() {
    return {
      textFound: false,
      replaceWords: replaceWords,
      dataList: {},
      text_value: "",
      InputFile: null,
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

    arrangeList() {
      this.dataList = Object.entries(this.replaceWords);
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
      let articleText_new = this.$refs.textArea.value;
      this.text_value = this.$refs.textArea.value;

      this.dataList.forEach((element) => {
        if (this.text_value.includes(element[0])) {
          articleText_new = this.text_value.replace(
            new RegExp(element[0], "g"),
            element[1]
          );
          this.text_value = articleText_new;
          this.textFound = true;
        }
      });
    },

    addExcelFile() {
      const fileReader = new FileReader();
      console.log("this.InputFile", this.InputFile);
      fileReader.onload = (ev) => {
        const workbook = XLSX.read(ev.target.result, {
          type: "binary",
        });
        const wsname = workbook.SheetNames[0];
        const ws = XLSX.utils.sheet_to_json(workbook.Sheets[wsname]);
        console.log("ws", ws); // 轉換成json的數據
        // const m = dealExcel(ws); // ...對數據進行自己需要的操作
        // console.log(m);
      };
      if (this.InputFile != null) {
        fileReader.readAsBinaryString(this.InputFile);
      }
    },
  },
};
</script>

<style lang="scss">
.no-resize {
  resize: none;
}

.HomeView {
  .horizontal_list {
    display: flex;
    list-style: none; /* 去除默认的列表样式 */
    padding: 0; /* 去除默认的内边距 */
    div {
      margin-right: 10px; /* 项之间的间距 */
    }
  }
}

.file-upload-input {
  display: none;
}

.file-upload-zone {
  margin: 150px auto;
  display: flex;
  flex-direction: column;
  box-sizing: border-box;
  padding: 8px;
  width: 400px;
  height: 200px;
  background-color: #f0f0f0;
  border-radius: 5px;
  border: 1px dashed gray;
  text-transform: capitalize;
  box-shadow: 1px 1px 10px gray;
}

.file-upload__btn {
  flex-grow: 1;
  padding: 2px 7px;
  color: black;
  width: 100px;
  height: 6px;
  text-align: center;
  background-color: #66b3ff;
  border-radius: 3px;
  text-decoration: none;
  border: 1px solid gray;
}

.file-upload__btn:active {
  transform: translate(0.5px, 0.5px);
}

.file-upload__name {
  /* 	flex-grow: 2; */
  font-size: 14px;
  color: blue;
}

.file-upload__text-container {
  flex-grow: 6;
  display: flex;
  align-items: center;
  justify-content: center;
}

.drop-area {
  width: 300px;
  height: 200px;
  border: 2px dashed #ccc;
  text-align: center;
  padding: 20px;
}
</style>
