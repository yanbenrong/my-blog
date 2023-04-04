<!--
 * @Author: YanBenrong
 * @LastEdit: YanBenrong
 * @LastEditors: YanBenrong
 * @Description: 
 * @params: 
 * @Date: 2023-03-30 16:24:57
 * @LastEditTime: 2023-04-04 22:03:18
-->
<template>
  <div class="category" v-if="headers.length > 0">
    <ul class="list">
      <li class="header" v-for="item in headers">
        <a :href="item.link" class="header-h2" v-if="item.level === 2">{{
          item.title
        }}</a>
        <ul v-if="item.level === 3">
          <li class="header">
            <a :href="item.link" :class="['header-h3', { showIndent: showIndent }]">{{ item.title }}</a>
          </li>
        </ul>
      </li>
    </ul>
  </div>
  <div class="totop" @click="toTop">
    <!-- <div class="sj"></div> -->
    <img class="hj" src="../../../public/rocket.png" alt="">
  </div>
</template>
<script lang="ts" setup>
import { useData, onContentUpdated } from "vitepress";
import { shallowRef, ref } from "vue";
import { getHeaders } from "../utils";

const { frontmatter, theme } = useData();
const headers = shallowRef<any>([]);
const showIndent = ref(false);
onContentUpdated(() => {
  headers.value = getHeaders(frontmatter.value.outline ?? theme.value.outline);
  showIndent.value = headers.value.some((header) => {
    return header.level === 2;
  });
});
const toTop = () => {
  window.scrollTo({
    left: 0,
    top: 0,
  })
}
</script>
<style scoped>
.category {
  width: 20rem;
  background: var(--vp-c-bg);
  box-shadow: 6px 6px var(--vp-c-brand);
  border: 4px solid #708090;
  color: var(--vp-c-brand-light);
  overflow-y: auto;
  max-height: 300px;
  margin-bottom: 20px;
}

.list {
  padding-left: 1.25em;
  margin: 1rem 0;
  line-height: 1.7;
  list-style-type: none;
  box-sizing: border-box;
}

.showIndent {
  padding-left: 1rem;
}

ul {
  list-style-type: none;
}

.header {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.totop {
  position: fixed;
  right: 50px;
  bottom: 70px;
  z-index: 100;
  color: #fff;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background-color: var(--vp-c-brand-dark);

  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
}

.sj {
  width: 0;
  height: 0;
  border: 10px solid transparent;
  border-bottom: 12px solid #fff;
  transform: translateY(-5px);
}

.hj {
  width: 25px;
  height: 25px;
}

@media (min-width: 768px) {
  .category {
    max-height: 400px;
  }

  .totop {
    right: 150px;
    bottom: 150px;
  }
}

@media (min-width: 1024px) {
  .category {
    max-height: 450px;
  }

  .totop {
    right: 150px;
    bottom: 150px;
  }

}

@media (min-width: 1400px) {
  .category {
    position: fixed;
    right: 20px;
    max-height: 490px;
  }

  .totop {
    right: 150px;
    bottom: 150px;
  }

}
</style>
