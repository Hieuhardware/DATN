<template>
  <div class="sign-in">
    <div class="sign-in-main">
      <div class="signin-header">Đăng nhập</div>
      <div class="singin-content">
        <div class="form">
          <div class="signin-content--item">
            <i class="fas fa-envelope"></i>
            <span class="title">Email:</span>
            <input
              type="text"
              class="input"
              placeholder="Nhập email của bạn"
              v-model="email"
            />
          </div>
          <div class="signin-content--item">
            <i class="fas fa-key"></i>
            <span class="title">Mật khẩu:</span>
            <input
              type="password"
              class="input"
              placeholder="Nhập mật khẩu"
              v-model="password"
            />
          </div>
          <div
            class="signin-content--item"
            style="display: flex; justify-content: center"
          >
            <button class="button" @click="SignIn()">Đăng Nhập</button>
          </div>
        </div>
      </div>
      <div class="signin-footer">
        Chưa có tài khoản? <router-link to="/signup">Đăng kí</router-link>
      </div>
      <ErrorPopup :title="title" @close="close" v-if="hasError"></ErrorPopup>
    </div>
  </div>
</template>
<script>
import api from "@/js/api";
import ErrorPopup from "@/components/Bases/BasePopup/ErrorPopup";
export default {
  components: {
    ErrorPopup,
  },
  data() {
    return {
      email: "",
      password: "",
      title: "",
      hasError: false,
    };
  },
  methods: {
    SignIn() {
      if (this.email && this.password) {
        this.$axios.get(`${api.CustomerApi}/${this.email}`).then((res) => {
          if (res.data) {
            if (res.data.password == this.password) {
              this.$store.commit("TOGGLE_CUSTOMER", res.data);
              this.$router.replace({ path: "/content" });
              window.scrollTo({ top: 0, behavior: "smooth" });
            } else {
              this.title = "Mật khẩu không chính xác.";
              this.hasError = true;
            }
          } else {
            this.title = "Email không chính xác.";
            this.hasError = true;
          }
        });
      } else {
        this.title = "Vui lòng nhập Email và mật khẩu.";
        this.hasError = true;
      }
    },
    close() {
      this.hasError = false;
    },
  },
};
</script>
<style scoped>
.sign-in {
  width: 100%;
  display: flex;
  justify-content: center;
}
.sign-in-main {
  width: 1600px;
}
.signin-header {
  height: 60px;
  font-weight: 600;
  font-size: 20px;
  padding: 0 24px;
  line-height: 60px;
  color: #ef3073;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
}

.signin-content--item {
  display: flex;
  justify-items: center;
  margin: 24px 0;
}
.signin-content--item i {
  line-height: 40px;
  padding-right: 8px;
}
.signin-content--item span {
  width: 140px;
  line-height: 40px;
}
.signin-content--item input {
  width: 400px;
  border: 1px solid #000;
}
.signin-content--item input:focus,
input:hover {
  border: 2px solid #ef3073;
}
.signin-footer {
  height: 60px;
  padding: 0 24px;
  line-height: 60px;
  font-size: 20px;
  border-top: 2px solid #ccc;
}
.form {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
</style>