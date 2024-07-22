<script>
export default {
  methods: {
    logout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    }
  }
}
</script>

<template>
<div>
  <el-button type="info" @click="logout">退出</el-button>
</div>
</template>

<style lang="less" scoped>

</style>
