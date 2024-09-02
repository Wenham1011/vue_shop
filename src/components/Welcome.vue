<script>
export default {
  data() {
    return {}
  },
  mounted() {
  },
  methods: {}
}
</script>

<template>
  <div class="welcome_container">
    <div class="welcome_title">
      <h1>Welcome to <span>Electronic business</span> system</h1>
    </div>
  </div>
</template>

<style scoped lang="less">
.welcome_container {
  background-image: url("../assets/cozy_sun.png");
  background-size: cover;  // 覆盖整个容器，保持宽高比
  background-position: center;
  height: 100vh;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.welcome_title h1 {
  text-align: center;
  font-size: 50px;
  color: rgb(19,57,100);

}

.welcome_title h1 span {
  color: rgb(255,106,91);
}
</style>
