<script setup>

</script>

<template>
<div class="content">
  <el-empty :image-size="500" description=" ">404啦~</el-empty>
  <p>找不到该页面,请<a href="/#/welcome">点击此处</a>返回首页</p>
</div>
</template>

<style scoped lang="less">
.content .el-empty {
  font-size: 50px;
}

.content p {
  text-align: center;
}

.content p>a {
  color: rgb(83,124,121); /* 或者设置为具体的颜色，如 #000 表示黑色 */
  text-decoration: none; /* 移除下划线 */
}

.content p >a:hover {
  color: rgb(251,84,48);
  cursor: pointer;
}
</style>
