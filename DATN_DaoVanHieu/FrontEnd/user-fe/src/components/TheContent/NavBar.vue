<template>
  <div
    class="navbar"
    data-aos="flip-left"
    data-aos-easing="ease-out-cubic"
    data-aos-duration="1000"
  >
    <div class="navbar__title">
      <i class="fas fa-bars"></i>
      <span
        style="font-size: 20px; font-weight: 500"
        data-aos="fade-right"
        data-aos-duration="2000"
        >DANH SÁCH</span
      >
    </div>
    <div
      class="navbar__item"
      v-for="item in list"
      :key="item.id"
      @click="$emit('changeCategory', item)"
      data-aos="fade-left"
      data-aos-duration="1500"
    >
      {{ item.name }}
    </div>
  </div>
</template>
<script>
import api from "@/js/api";
export default {
  data() {
    return {
      list: [],
    };
  },
  created() {
    this.getItems();
  },
  methods: {
    getItems() {
      this.$axios.get(api.CategoryApi).then((res) => {
        this.list = res.data;
      });
    },
  },
};
</script>
<style scoped>
.navbar {
  min-width: 232px;
  border: 1px solid #ccc;
  background: #fff;
  height: 100%;
}
.navbar__title {
  display: flex;
  gap: 12px;
  height: 50px;
  padding: 0 0 0 24px;
  align-items: center;
}
.navbar__item {
  display: block;
  width: 100%;
  height: 50px;
  line-height: 50px;
  text-align: left;
  padding: 0 0 0 24px;
  border-top: 1px dotted #ccc;
  transition: all linear 1s;
  cursor: pointer;
  font-size: 14px;
  text-transform: uppercase;
}
.navbar__item:hover {
  background: #ef3073;
  color: #fff;
}
</style>