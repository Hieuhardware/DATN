<template>
  <div class="list-product">
    <div class="list-product-title">
      {{ title }}
    </div>
    <div class="list-product-content">
      <div class="category-description" v-if="description">
        {{ description }}
      </div>
      <div v-if="!items.length" class="no-data">
        Hiện tại chưa có sản phẩm để hiển thị
      </div>
      <div class="card" v-for="(item, index) in items" :key="index" v-else>
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
      <div class="pagination">
        <Pagination
          ref="pagination"
          :itemCount="totalItems"
          :maxDisplayPage="3"
          :page="PageNumber"
          :perPage="18"
          @pageChange="pageChange"
        ></Pagination>
      </div>
    </div>
    <ErrorPopup :title="mesage" @close="close" v-if="hasError"></ErrorPopup>
    <Loader v-if="hasLoader"></Loader>
    <ToastMesage
      v-if="hasToast"
      :mesage="mesage"
      @closeToast="closeToast"
    ></ToastMesage>
  </div>
</template>
<script>
import defaultAvatar from "../../assets/image/img_default.jpg";
import Pagination from "@/components/Bases/Pagination";
import Loader from "@/components/Bases/Loader";
import ToastMesage from "@/components/Bases/ToastMesage";
import ErrorPopup from "@/components/Bases/BasePopup/ErrorPopup";
import api from "@/js/api";
export default {
  components: {
    Pagination,
    Loader,
    ToastMesage,
    ErrorPopup,
  },
  props: ["title", "items", "description", "totalItems"],
  data() {
    return {
      avatar: defaultAvatar,
      TotalRecord: this.totalItems,
      PageNumber: 1,
      hasLoader: false,
      hasToast: false,
      hasError: false,
      mesage: "",
    };
  },
  created() {},
  computed: {
    getCustomer() {
      return this.$store.state.customer;
    },
  },
  methods: {
    resetPage() {
      this.$refs.pagination.resetPage();
    },
    pageChange(value) {
      this.PageNumber = value;
      this.$emit("pageChange", value);
    },
    viewDetail(value) {
      this.$emit("viewDetail", value);
    },
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
    priceFormat(value) {
      if (!value) return "";
      return value.toLocaleString("vi-VN", {
        style: "currency",
        currency: "VND",
      });
    },
    closeToast() {
      this.hasToast = false;
    },
    close() {
      this.hasError = false;
    },
  },
};
</script>
<style scoped>
.list-product {
  width: 100%;
}
.list-product-title {
  height: 40px;
  border-bottom: 2px solid #333;
  font-size: 32px;
  font-weight: 600;
}
.list-product-content {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  padding: 12px 0 0 0;
}
.card {
  flex-basis: 23.3%;
  height: 270px;
  width: 228px;
  background: #fff;
  border: 1px solid #ccc;
  position: relative;
  padding-bottom: 40px;
  z-index: 1;
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
  height: 165px;
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
.no-data {
  text-align: center;
  font-size: 24px;
  color: red;
  width: 100%;
}
.pagination {
  display: flex;
  width: 100%;
  justify-content: center;
}
</style>