<template>
  <div class="navbar">
    <div class="logo">
      <img
        src="../../assets/image/logowebsitefinal.png"
        width="200"
        height="200"
        alt=""
      />
    </div>
    <div class="navbar-items">
      <router-link to="/dashboard" class="navbar-item">
        <i class="fas fa-chart-line icon"></i>
        <span class="title">Trang chủ</span>
      </router-link>
      <router-link to="/category" class="navbar-item">
        <i class="fas fa-grip-lines icon"></i>
        <span class="title">Danh mục</span>
      </router-link>
      <router-link to="/product" class="navbar-item">
        <i class="fas fa-store icon"></i>
        <span class="title">Hàng hóa</span>
      </router-link>
      <router-link to="/customer" class="navbar-item">
        <i class="fas fa-users icon"></i>
        <span class="title">Khách hàng</span>
      </router-link>
      <router-link to="/order" class="navbar-item">
        <i class="fas fa-shopping-cart icon"></i>
        <span class="title">Đơn hàng</span>
      </router-link>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      isActive: false,
    };
  },
};
</script>
<style scoped>
.navbar {
  display: flex;
  flex-direction: column;
  background-color: #0091d5;
  min-width: 250px;
  max-width: 250px;
  min-height: 700px;
}
.logo {
  height: 100px;
  width: 250px;
  border-bottom: 2px solid #eee;
  display: flex;
  align-items: center;
  justify-content: center;
}
.navbar-item {
  display: block;
  opacity: 0.8;
  height: 60px;
  padding: 4px 12px;
  display: flex;
  gap: 24px;
  align-items: center;
  border-bottom: 1px solid #eee;
  transition: all linear 0.5s;
}

.title {
  color: #fff;
}
.icon {
  color: #fff;
}
.navbar-item:hover {
  opacity: 1;
  background-color: #ccc;
  color: #fff;
  cursor: pointer;
}
.router-link-active {
  background-color: #a5d8dd;
}
</style>