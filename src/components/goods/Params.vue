<script>
export default {
  data() {
    return {}
  },
  created() {
  },
  methods: {}
}
</script>

<template>
  <div>
    <!--面包屑导航区-->
    <el-breadcrumb>
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>参数列表</el-breadcrumb-item>
    </el-breadcrumb>

    <!--卡片视图-->
    <el-card>
      <el-alert
        title="注意：只允许为第三级分类进行设置"
        :closable="false"
        show-icon
        type="warning">
      </el-alert>
      <!--黄色警告区-->
      <el-row>
        <el-col style="margin-top: 15px">
          <h3 style="color: rgb(11,81,205)">该功能待开发,请欣赏舒适风景~~</h3>
        </el-col>
        <el-col class="setImg">
          <img src="../../assets/cozy_night.png" alt="商品分类参数的略过图">
          <!--商品分类选择的级联选择器-->
        </el-col>
      </el-row>

    </el-card>
  </div>
</template>

<style scoped lang="less">
.setImg {
  margin-top: 10px;
  img {
    object-fit: cover;
    height: 100%;
    width: 100%;
  }
}
</style>
