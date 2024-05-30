<template>
  <div class="product-detail">
    <div class="product-info">
      <div class="product-info-main">
        <div class="product-image">
          <img :src="item.image || avatar" alt="" />
        </div>

        <div class="product-property">
          <div class="title">
            {{ item.name }}
          </div>
          <table class="table-detail">
            <tr>
              <td class="table-title">Mã sản phẩm</td>
              <td>{{ item.id }}</td>
            </tr>
            <tr>
              <td class="table-title">Xuất xứ</td>
              <td>{{ item.origin }}</td>
            </tr>
            <tr>
              <td class="table-title">Đơn vị tính</td>
              <td>{{ item.unit }}</td>
            </tr>
            <tr>
              <td class="table-title">Tình trạng</td>
              <td>{{ item.amount > 0 ? "Còn hàng" : "Hết hàng" }}</td>
            </tr>
          </table>
          <p
            style="
              font-size: 24px;
              color: #dc3545;
              padding: 24px 12px;
              font-weight: 600;
            "
          >
            {{ priceFormat(item.price) }}
          </p>
          <div class="action">
            <button class="button" @click="addCart(item)">
              Thêm vào giỏ hàng
            </button>

            <button
              class="button button-pay"
              @click="handleAddToCartAndNavigate(item)"
            >
              Mua ngay
            </button>
          </div>
        </div>
      </div>
      <div class="detail-des">
        <div
          class="product-info-title"
          data-aos="fade-left"
          data-aos-easing="ease-out-cubic"
          data-aos-duration="1000"
        >
          Thông tin chi tiết sản phẩm
        </div>
        <div
          class="main-description"
          v-html="item.description"
          ref="container"
        ></div>
      </div>
    </div>
    <div class="product-related">
      <div class="related-main">
        <div class="related-title">
          <span>SẢN PHẨM LIÊN QUAN</span>
        </div>
        <div class="card" v-for="(item, index) in items" :key="index">
          <div class="card-content">
            <div class="card-title">
              <span @click="viewDetail(item)">{{ item.name }}</span>
            </div>
            <div class="product-image" @click="viewDetail(item)">
              <img :src="item.image || avatar" alt="" />
            </div>
          </div>
          <div class="card-footer">
            <span style="font-size: 20px; font-weight: 600"
              >{{ priceFormat(item.price) }}
            </span>
          </div>
          <button class="btn-add-to-cart" @click="addCart(item)">
            Cho vào giỏ hàng
          </button>
        </div>
      </div>
    </div>
    <ErrorPopup :title="mesage" @close="close" v-if="hasError"></ErrorPopup>
    <Loader v-if="hasLoader"></Loader>
    <ToastMesage
      v-if="hasToast"
      :mesage="mesage"
      @closeToast="close"
    ></ToastMesage>
  </div>
</template>
<script>
import defaultAvatar from "../assets/image/img_default.jpg";
import ErrorPopup from "@/components/Bases/BasePopup/ErrorPopup";
import Loader from "@/components/Bases/Loader";
import ToastMesage from "@/components/Bases/ToastMesage";
import api from "@/js/api";
export default {
  props: ["item"],
  components: {
    ErrorPopup,
    Loader,
    ToastMesage,
  },
  data() {
    return {
      avatar: defaultAvatar,
      items: [],
      hasError: false,
      hasLoader: false,
      hasToast: false,
      mesage: "",
    };
  },
  created() {
    this.findProductRelated();
  },
  mounted() {
    this.applyCssToImages();
  },
  computed: {
    getCustomer() {
      return this.$store.state.customer;
    },
  },
  methods: {
    addCart(item) {
      let customer = this.getCustomer;
      if (customer) {
        this.$axios.get(`${api.CartApi}/${customer.id}`).then((res) => {
          if (
            res.data.some(
              (c) => c.cus_id == customer.id && c.prod_id == item.id
            )
          ) {
            this.mesage = "Sản phẩm này đã có trong giỏ hàng";
            this.hasError = true;
            return;
          }
          let cart = {
            cus_id: customer.id,
            prod_id: item.id,
            quantity: 1,
          };
          this.hasLoader = true;
          this.$axios
            .post(api.CartApi, cart)
            .then((res) => {
              if (res.status == 201) {
                this.mesage = "Thêm vào giỏ hàng thành công.";
                this.hasToast = true;
                setTimeout(() => {
                  this.hasToast = false;
                }, 3000);
                this.$axios.get(`${api.CartApi}/${customer.id}`).then((res) => {
                  this.$store.commit("CHANGE_CART", res.data.length);
                });
              }
            })
            .finally(() => (this.hasLoader = false));
        });
      } else {
        this.$router.replace({ path: "/signin" });
      }
    },
    findProductRelated() {
      if (this.item.name) {
        var same = this.item.name.split(" ")[0];
        try {
          this.$axios.get(`${api.ProductApi}/search/${same}`).then((res) => {
            this.items = res.data.data
              .filter((item) => item.name !== this.item.name)
              .slice(0, 4);
            this.number = res.data.data.length;
            this.total = res.data.total;
          });
        } catch (res) {
          console.log(res.data);
        }
      }
    },
    priceFormat(value) {
      if (!value) return "";
      return value.toLocaleString("vi-VN", {
        style: "currency",
        currency: "VND",
      });
    },
    applyCssToImages() {
      const container = this.$refs.container;
      if (container) {
        const images = container.querySelectorAll("img");
        images.forEach((image) => {
          image.style.maxWidth = "500px";
          image.style.height = "auto";
        });
      }
    },
    handleAddToCartAndNavigate(item) {
      let customer = this.getCustomer;
      if (customer) {
        this.$axios.get(`${api.CartApi}/${customer.id}`).then((res) => {
          if (
            res.data.some(
              (c) => c.cus_id == customer.id && c.prod_id == item.id
            )
          ) {
            return;
          }
          let cart = {
            cus_id: customer.id,
            prod_id: item.id,
            quantity: 1,
          };
          this.$axios
            .post(api.CartApi, cart)
            .then((res) => {
              if (res.status == 201) {
                setTimeout(() => {}, 3000);
                this.$axios.get(`${api.CartApi}/${customer.id}`).then((res) => {
                  this.$store.commit("CHANGE_CART", res.data.length);
                });
              }
            })
            .finally();
        });
      } else {
        this.$router.replace({ path: "/signin" });
      }
      setTimeout(() => {
        this.$router.push("/cart");
      }, 300);
    },
    viewDetail(value) {
      this.$emit("viewDetail", value);
    },
    close() {
      this.hasToast = false;
      this.hasError = false;
    },
  },
};
</script>
<style scoped>
.title {
  height: 50px;
  font-size: 20px;
  padding: 0 0 8px 0;
  color: #000;
  align-content: center;
}
.product-detail {
  width: 100%;
  display: flex;
  flex-direction: row;
  gap: 24px;
}

.product-info {
  width: 100%;
  background: #fff;
}
.product-info-main {
  display: flex;
  gap: 12px;
  padding: 12px 24px;
  justify-content: space-between;
}

.product-info-title {
  color: #ef3073;
  border-top: 2px solid #ef3073;
  padding: 8px 0 0 12px;
  text-transform: uppercase;
  font-family: Roboto;
  font-weight: 600;
}

.main-description {
  padding: 12px 24px;
}

.table-detail {
  border-collapse: collapse;
}
.table-detail td {
  border: 1px solid #ccc;
  min-width: 100px;
  padding: 8px 12px;
}
.table-title {
  font-size: 14px;
  font-weight: 600;
  background-color: #ebebeb;
}
.store-title {
  margin: 12px 0;
}

.related-main {
  border-top: 2px solid #ef3073;
}

.related-title {
  padding: 8px 8px;
  font-weight: 600;
  font-family: Roboto;
  color: #ef3073;
  display: flex;
  justify-content: center;
}
.related-title span {
  font-size: 16px;
}
.card {
  height: 270px;
  width: 198px;
  background: #fff;
  border: 1px solid #ccc;
  position: relative;
  padding-bottom: 40px;
  z-index: 1;
  margin-bottom: 16px;
}
.card-content {
  overflow: hidden;
  padding: 0 12px;
  margin-top: 16px;
}
.card-title {
  padding: 0 0 12px 0;
  height: 40px;
}

.card-title span {
  font-size: 13px;
  font-weight: 100;
  color: #000;
  font-weight: 500;
}
.card-title:hover {
  color: #ff0000;
  cursor: pointer;
}
.product-image {
  width: auto;
  padding: 4px 0 4px 0;
  border-bottom: 1px dotted #ccc;
  border-top: 1px dotted #ccc;
  display: flex;
  align-content: center;
  justify-content: center;
  cursor: pointer;
}
.product-image img {
  max-height: 100%;
  max-width: 100%;
  opacity: 0.8;
}
.product-image:hover img {
  opacity: 1;
}
.card-footer {
  display: flex;
  justify-content: space-between;
  padding: 12px;
  width: 100%;
  position: absolute;
  bottom: 0;
}
.card-footer span {
  font-size: 16px !important;
  color: #ff0000;
}
.btn-add-to-cart {
  display: none;
  position: absolute;
  bottom: 50px;
  left: 50%;
  width: 72%;
  padding: 4px 8px;
  border: 1px solid #ccc;
  transform: translateX(-50%);
  background-color: #ef3073;
  font-size: 14px;
  color: white;
  border: none;
  cursor: pointer;
}

.btn-add-to-cart:hover {
  background-color: #fff;
  color: rgb(215, 120, 120);
  border: 1px solid #ccc;
}
.card:hover .btn-add-to-cart {
  display: block;
}
</style>