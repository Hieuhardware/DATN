<template>
  <div class="mainn">
    <div class="banner">
      <div class="logo" data-aos="zoom-in-right" data-aos-duration="1500">
        <router-link to="/content">
          <img
            class="logo-img"
            src="../../assets/image/logowebsitefinal.png"
            width="50"
            height="50"
            alt=""
          />
        </router-link>
      </div>
      <div class="search-box">
        <div class="search">
          <input
            class="input"
            type="search"
            placeholder="Tìm kiếm..."
            v-model="search"
            @input="findProduct()"
            autocomplete="false"
          />
          <div class="search-result" v-if="search">
            <div class="searchTitle" v-if="!items.length">
              Không có kết quả nào trùng khớp
            </div>
            <div class="searchTitle" v-else>
              Hiển thị {{ number }}/{{ total }} kết quả tìm kiếm trùng khớp.
            </div>
            <div @click="closeSearchBox()">
              <router-link
                :to="'/detail/' + item.id"
                class="search-result-item"
                v-for="item in items"
                :key="item.id"
                data-aos="fade-left"
                data-aos-easing="ease-out-cubic"
                data-aos-duration="1500"
              >
                <img :src="item.image || avatar" alt="" />
                <div class="name">{{ item.name }}</div>
                <div class="price">{{ item.price }}đ</div>
              </router-link>
            </div>
          </div>
        </div>
        <button class="button">
          <div class="btn-search" @click="closeSearch()">
            <i class="fas fa-search icon"></i>
          </div>
        </button>
      </div>
      <router-link to="/cart" class="cart" v-if="hasCustomer">
        <img src="../../assets/image/icons-cart.png" alt="" class="cart-icon" />
        <div class="info">
          <span>{{ item }}</span>
        </div>
      </router-link>

      <ErrorPopup @close="close" :title="title" v-if="hasError"></ErrorPopup>
    </div>
  </div>
</template>
<script>
import ErrorPopup from "@/components/Bases/BasePopup/ErrorPopup";
import defaultAvatar from "../../assets/image/img_default.jpg";
import api from "@/js/api";
export default {
  components: {
    ErrorPopup,
  },
  data() {
    return {
      item: "0",
      items: [],
      total: 0,
      number: 0,
      search: "",
      hasError: false,
      title: "",
      avatar: defaultAvatar,
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
    cart() {
      return this.$store.state.cart;
    },
  },
  watch: {
    customer: function (value) {
      if (value) {
        this.$axios.get(`${api.CartApi}/${value.id}`).then((res) => {
          this.item = res.data.length;
          this.$store.commit("CHANGE_CART", res.data.length);
        });
      }
    },
    cart: function (value) {
      this.item = value;
    },
  },
  methods: {
    findProduct() {
      if (this.search) {
        this.$axios
          .get(`${api.ProductApi}/search/${this.search}`)
          .then((res) => {
            console.log(res.data);
            this.items = res.data.data;
            this.number = res.data.data.length;
            this.total = res.data.total;
          });
      }
    },
    closeSearchBox() {
      setTimeout(() => {
        this.search = "";
      }, 100);
    },
    closeSearch() {
      if (this.search) {
        this.$router.push({ path: `/listdetail/${this.search}` });
        setTimeout(() => {
          this.search = "";
        }, 100);
      } else {
        this.title = "Vui lòng điền thông tin tìm kiếm.";
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
.mainn {
  width: 100%;
  border-bottom: 1px solid #ccc;
  height: 100px;
  align-content: center;
}
.banner {
  width: 1600px;
  padding: 0 48px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  z-index: 9999;
  margin: auto;
}

.logo-img {
  width: 200px;
  height: auto;
}

.search-box {
  display: flex;
  gap: 12px;
}
.search {
  position: relative;
  z-index: 9999;
  /* border: blue 1px solid; */
}
.input {
  width: 400px;
  /* padding-left: 60px;
  background-image: url("../../assets/image/magnifier.png");
  background-position: 12px center;
  background-size: 32px 32px;
  background-repeat: no-repeat; */
}
.input:hover,
.input:focus {
  border: 1px solid #ef3073;
}
.search-result {
  position: absolute;
  top: 100%;
  left: 0;
  width: 100%;
  background: #fff;
  border-radius: 4px;
  z-index: 9999;
}
.searchTitle {
  color: #757e72;
  font-weight: 600;
  font-size: 16px;
  height: 30px;
  line-height: 30px;
  padding: 0 12px;
}
.search-result-item {
  min-height: 40px;
  display: flex;
  gap: 12px;
  align-items: center;
  width: 100%;
  padding: 4px 12px;
  color: #333;
  transition: all linear 0.6s;
}
.search-result-item:hover {
  background: #ef3073;
  color: #fff;
}
.search-result-item img {
  height: 36px;
  width: 36px;
  border-radius: 100%;
}
.search-result-item .name {
  flex-grow: 1;
  text-overflow: ellipsis;
}

.cart {
  padding: 5px;
  border: 2px solid #339af0;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-content: center;
  position: relative;
}

.cart-icon {
  margin: 4;
}

.info {
  position: absolute;
  top: -10px;
  right: -10px;
  border-radius: 44%;
  padding: 4px;
  color: #fff;
  background-color: #339af0;
}

.icon {
  color: #fff;
}
</style>