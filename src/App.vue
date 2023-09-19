<template>
  <div>
    <ul class="app-horizontal_list">
      <li><router-link to="/">轉換和諧字</router-link></li>
      <li><router-link to="/manage">管理資料庫</router-link></li>
    </ul>
    <router-view></router-view>
  </div>
</template>
<script>
export default {
  name: "App",
  components: {},

  data() {
    return {};
  },

  mounted() {},

  methods: {},
};
</script>

<style lang="scss">
.app {
  &-horizontal_list {
    display: flex;
    list-style: none; /* 去除默认的列表样式 */
    padding: 0; /* 去除默认的内边距 */
    li {
      margin-right: 10px; /* 项之间的间距 */
    }
  }
}
</style>
