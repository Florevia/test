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
  <div class="container" v-if="isLoading">
    <div class="loading-text">
      <span style="--i: 0">正</span>
      <span style="--i: 1">在</span>
      <span style="--i: 2">加</span>
      <span style="--i: 3">载</span>
      <span style="--i: 4">.</span>
      <span style="--i: 5">.</span>
      <span style="--i: 6">.</span>
    </div>
  </div>
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
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #f5f5f5;
}
.loading-text {
  display: flex;
  font-size: 28px;
  font-weight: 600;
  color: #409eff;
}
.loading-text span {
  display: inline-block;
  animation: wave 1s ease-in-out infinite;
  animation-delay: calc(var(--i) * 0.1s);
}
@keyframes wave {
  0%,
  100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-12px);
  }
}
.img {
  width: 100%;
  height: 100%;
}
.fail {
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
