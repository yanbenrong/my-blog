<!--
 * @Author: YanBenrong
 * @LastEdit: YanBenrong
 * @LastEditors: YanBenrong
 * @Description: 
 * @params: 
 * @Date: 2023-03-30 16:24:57
 * @LastEditTime: 2023-03-31 00:17:06
-->
<template>
  <div class="shareCard">
    <h1 class="title">
      <span>Hi, I'm Yan Benrong 👋</span>
    </h1>
    <p class="description">
      This is my personal blog.
    </p>
  </div>
</template>

<script lang="ts" setup></script>
<style scoped>
.shareCard {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 0 15px;
  text-align: center;
}
.title {
  display: flex;
  align-items: center;
  font-size: 2.2em;
  font-weight: bold;
}
.description {
  margin: 1em 0;
}
</style>
