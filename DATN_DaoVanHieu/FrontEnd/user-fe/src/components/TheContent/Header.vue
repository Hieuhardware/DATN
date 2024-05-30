<template>
  <div class="header">
    <div class="hotline content-item">
      <p>
        <span style="font-weight: 600"
          >Hệ thống bán lẻ văn phòng phẩm trên toàn quốc</span
        >
      </p>
    </div>
    <div class="account" v-if="!hasCustomer">
      <router-link to="/signin" class="signin content-item">
        <i class="fas fa-sign-in-alt icon"></i>
        <p>Đăng nhập</p>
      </router-link>
      <router-link to="/signup" class="signup content-item">
        <i class="fas fa-user-plus icon"></i>
        <p>Đăng kí</p>
      </router-link>
    </div>
    <div class="account" v-else data-aos="fade-right" data-aos-duration="1500">
      <router-link to="/infomation" class="signin content-item">
        <i class="fas fa-user icon"></i>
        <p>{{ this.customer.name }}</p>
      </router-link>
      <div class="signin content-item">
        <i class="fas fa-sign-in-alt icon"></i>
        <p @click="signOut">Đăng xuất</p>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      item: "0",
    };
  },
  computed: {
    hasCustomer() {
      let customer = this.$store.state.customer;
      if (customer) {
        return true;
      }
      return false;
    },
    customer() {
      return this.$store.state.customer;
    },
  },
  methods: {
    signOut() {
      this.$store.commit("TOGGLE_CUSTOMER", "");
      if (this.$route.path !== "/content") {
        this.$router.replace({ path: "/content" });
      }
    },
  },
  watch: {
    customer: function () {},
  },
};
</script>
<style scoped>
.header {
  padding: 0 48px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  height: 40px;
  background: #eee;
  border-bottom: 1px solid #ccc;
  background: #ef3073;
  color: #fff;
}
.account {
  display: flex;
  gap: 24px;
}
.signin,
.signup {
  cursor: pointer;
}
.content-item {
  opacity: 0.8;
  transition: all linear 0.6s;
  color: #fff;
}
.icon {
  color: #fff;
}
.content-item:hover {
  opacity: 1;
}
</style>