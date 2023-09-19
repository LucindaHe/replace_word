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

    <div class="container">
      <div @click="addExcelFile">讀取excel</div>

      <input class="file-upload-input" name="upload-file" type="file" />

      <div class="file-upload-zone">
        <a href="#" class="file-upload__btn">upload file</a>
        <span class="file-upload__name"></span>
        <div class="file-upload__text-container">
          <p>drop files here</p>
        </div>
      </div>
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

    const fileInput = document.querySelector(".file-upload-input");
    const fileNameZone = document.querySelector(".file-upload__name");
    const fileBtn = document.querySelector(".file-upload__btn");
    const dropZone = document.querySelector(".file-upload-zone");

    fileInput.addEventListener("change", function (e) {
      console.log(e.target.files);
      const fileName = e.target.files[0].name;
      const idxDot = fileName.lastIndexOf(".") + 1;
      const extFile = fileName.substr(idxDot, fileName.length).toLowerCase();
      if (extFile === "xls" || extFile === "xlsx") {
        fileNameZone.textContent = fileName;
        this.InputFile = e.target.files[0];
        console.log("InputFile", this.InputFile);
      } else {
        fileNameZone.textContent = "formatError";
        this.InputFile = null;
        console.log("InputFile", this.InputFile);
      }
    });

    fileBtn.addEventListener("click", function () {
      fileInput.click();
    });

    dropZone.addEventListener("drop", function (e) {
      e.preventDefault();

      const fileName = e.dataTransfer.files[0].name;
      const idxDot = fileName.lastIndexOf(".") + 1;
      const extFile = fileName.substr(idxDot, fileName.length).toLowerCase();
      if (extFile === "xls" || extFile === "xlsx") {
        fileNameZone.textContent = fileName;
        this.InputFile = e.dataTransfer.files[0];
        console.log("InputFile", this.InputFile);
      } else {
        fileNameZone.textContent = "formatError";
        this.InputFile = null;
        console.log("InputFile", this.InputFile);
      }
    });

    dropZone.addEventListener("dragenter", function (e) {
      e.preventDefault();
    });

    dropZone.addEventListener("dragover", function (e) {
      e.preventDefault();
    });

    dropZone.addEventListener("dragleave", function (e) {
      e.preventDefault();
    });
  },

  methods: {
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
</style>
