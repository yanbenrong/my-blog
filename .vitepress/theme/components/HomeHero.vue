<template>
  <div class="pic">
    <img src="../../../public/donghua4.gif" alt="">

  </div>
</template>
<script lang="ts" setup>
import { onMounted } from "vue";
import lottie from "lottie-web";
onMounted(() => {
  return
  // storage lottie
  const svgContainer = document.getElementById("svgContainer");
  if (svgContainer) {
    return;
  }
  // created insertNode and add style
  const insertNode = document.createElement("div");
  insertNode.id = "svgContainer";
  insertNode.style.width = "300px";
  insertNode.style.margin = "0 auto";
  const pic = document.getElementsByClassName("pic")[0];
  pic.appendChild(insertNode);
  // created lottie
  const animItem = lottie.loadAnimation({
    container: insertNode,
    renderer: "svg",
    loop: true,
    path: "https://labs.nearpod.com/bodymovin/demo/markus/isometric/markus2.json",
  });
});
</script>
<style scoped>
.pic {
  text-align: center;
  height: 300px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.pic img{
  width: 201px;
  height: 177px;
}
</style>
