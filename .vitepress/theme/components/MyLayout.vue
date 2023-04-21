<!--
 * @Author: YanBenrong
 * @LastEdit: YanBenrong
 * @LastEditors: error: error: git config user.name & please set dead value or install git && error: git config user.email & please set dead value or install git & please set dead value or install git
 * @Description: 
 * @params: 
 * @Date: 2023-03-30 16:24:57
 * @LastEditTime: 2023-04-21 11:21:37
-->
<template>
  <Layout>
    <template #doc-before>
      <Title />
      <Category />
    </template>
    <template #doc-after>
      <!-- <Comments /> -->
    </template>
    <!-- Home slot-->
    <template #home-hero-before>
      <HomeHero />
    </template>
    <template #home-features-after>
      <Page />
    </template>
  </Layout>
  <!-- copywright -->
  <CopyWright />
</template>
<script lang="ts" setup>
import DefaultTheme from "vitepress/theme";
import HomeHero from "./HomeHero.vue";
import CopyWright from "./CopyWright.vue";
import Comments from "./Comments.vue";
import Page from "./Page.vue";
import Category from "./Category.vue";
import Title from "./Title.vue";
const { Layout } = DefaultTheme;

</script>
<style scoped></style>
