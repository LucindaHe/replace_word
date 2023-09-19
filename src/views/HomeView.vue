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

    <router-view></router-view>
  </div>
</template>

<script>
import replaceWords from "@/database/replaceWord.json";

export default {
  name: "HomeView",
  components: {},

  data() {
    return {
      textFound: false,
      replaceWords: replaceWords,
      dataList: {},
      text_value: "",
    };
  },

  mounted() {
    this.arrangeList();
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
</style>
