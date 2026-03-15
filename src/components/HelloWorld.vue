<script setup>
import { onMounted, ref } from "vue";
import url from "../assets/云南大理.png";
const count = ref(0);
const isLoading = ref(true);
const imgUrl = ref("");

function getImgUrl() {
  setTimeout(() => {
    imgUrl.value = url;
    isLoading.value = false;
  }, 3000);

  setTimeout(() => {
    imgUrl.value = "";
  }, 6000);
}

onMounted(() => {
  getImgUrl();
});
</script>

<template>
  <!-- 正常加载 -->
  <div class="container" v-if="isLoading">正在加载...</div>
  <div v-else-if="imgUrl">
    <img class="img" :src="imgUrl" alt="" />
  </div>
  <!-- 加载失败 -->
  <div v-else class="fail">
    <p>没有权限</p>
    <a href="https://www.baidu.com">点击登录</a>
  </div>
</template>
<style scoped>
.container {
  display: block;
  width: 100%;
  height: 100vh; /* 用 vh 确保有明确高度 */
  display: flex;
  justify-content: center; /* 水平居中 */
  align-items: center;
}
.img {
  width: 100%;
  height: 100%;
}
.fail {
  width: 100%;
  height: 100vh; /* 用 vh 确保有明确高度 */
  display: flex;
  justify-content: center; /* 水平居中 */
  align-items: center;
}
</style>
