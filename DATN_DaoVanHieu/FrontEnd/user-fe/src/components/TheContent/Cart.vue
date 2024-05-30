<template>
  <div class="cart">
    <div class="cart-main">
      <div class="cart-header">Giỏ hàng</div>
      <div class="cart-content">
        <NoData v-if="noData"></NoData>
        <table v-else>
          <thead>
            <tr>
              <th>#</th>
              <th>Hình ảnh</th>
              <th>Tên sản phẩm</th>
              <th>Đơn giá</th>
              <th>Đơn vị tính</th>
              <th>Số lượng</th>
              <th>Thành tiền</th>
              <th>Xóa</th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(item, index) in items"
              :key="item.id + index + item.name"
            >
              <td style="min-width: 40px">{{ index + 1 }}</td>
              <td
                style="min-width: 40px; display: flex; justify-content: center"
              >
                <img :src="item.image || avatar" alt="" />
              </td>
              <td style="min-width: 400px">{{ item.name }}</td>
              <td style="min-width: 100px">{{ priceFormat(item.price) }}</td>
              <td style="min-width: 120px">{{ item.unit }}</td>
              <td style="min-width: 110px">
                <input
                  type="number"
                  v-model="item.quantity"
                  @change="changeQuantity(item)"
                  @input="limitQuantity(item)"
                />
              </td>
              <td style="min-width: 120px">
                {{ priceFormat(item.price * item.quantity) }}
              </td>
              <td style="min-width: 40px; cursor: pointer">
                <i class="far fa-trash-alt" @click="deleteCartItem(item)"></i>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <div class="cart-footer">
        <button class="button">
          <router-link to="/content">Tiếp tục mua hàng</router-link>
        </button>
        <button class="button" @click="payment()">Thanh toán</button>
      </div>
      <Loader v-if="hasLoader"></Loader>
      <ErrorPopup @close="close" :title="title" v-if="hasError"></ErrorPopup>
      <QuestionPopup
        :title="title"
        @close="close"
        @yes="yes"
        v-if="hasQuestion"
      ></QuestionPopup>
      <ToastMesage
        :mesage="title"
        @closeToast="closeToast"
        v-if="hasToast"
      ></ToastMesage>
    </div>
  </div>
</template>
<script>
import NoData from "@/components/Bases/NoData";
import defaultAvatar from "../../assets/image/img_default.jpg";
import Loader from "@/components/Bases/Loader";
import ErrorPopup from "@/components/Bases/BasePopup/ErrorPopup";
import QuestionPopup from "@/components/Bases/BasePopup/QuestionPopup";
import ToastMesage from "@/components/Bases/ToastMesage";
import api from "@/js/api";
export default {
  components: {
    Loader,
    QuestionPopup,
    ToastMesage,
    ErrorPopup,
    NoData,
  },
  data() {
    return {
      totalMoney: 0,
      noData: false,
      items: [],
      avatar: defaultAvatar,
      isPayMent: false,
      customer: {},
      hasLoader: false,
      hasQuestion: false,
      hasError: false,
      hasToast: false,
      title: "",
      selectedItem: {},
    };
  },
  created() {
    this.customer = this.getCustomer;
    if (!this.customer) {
      this.$router.replace({ path: "/content" });
    }
    this.getCart();
  },
  computed: {
    getCustomer() {
      return this.$store.state.customer;
    },
  },
  methods: {
    payment() {
      if (!this.items.length) {
        this.title =
          "Không có sản phẩm nào trong giỏ hàng, vui lòng thêm hàng vào giỏ trước khi đặt.";
        this.hasError = true;
        return;
      }
      this.$router.replace({ path: "/purchaseorder" });
    },
    getCart() {
      this.hasLoader = true;
      this.$axios
        .get(`${api.CartApi}/${this.customer.id}`)
        .then((res) => {
          this.items = res.data;
          if (this.items.length === 0) {
            this.noData = true;
          } else {
            this.noData = false;
          }
          this.$store.commit("CHANGE_CART", this.items.length);
        })
        .finally(() => (this.hasLoader = false));
    },
    priceFormat(value) {
      if (!value) return "";
      return value
        .toLocaleString("vi-VN", {
          style: "currency",
          currency: "VND",
          minimumFractionDigits: 0,
          maximumFractionDigits: 0,
        })
        .replace("₫", "");
    },
    limitQuantity(item) {
      const maxQuantity = item.maxQuantity; // Lấy giá trị tối đa từ dữ liệu hoặc từ nơi nào đó

      // Kiểm tra nếu số lượng mới vượt quá số lượng tối đa
      if (item.quantity > maxQuantity) {
        // Đặt lại giá trị của số lượng về giá trị tối đa
        item.quantity = maxQuantity;
      }
    },
    changeQuantity(item) {
      if (item.quantity == 0) {
        this.selectedItem = item;
        this.yes();
      }
      this.hasLoader = true;
      this.$axios
        .put(api.CartApi, item)
        .then(() => {})
        .catch((error) => {
          if (error.response && error.response.status === 400) {
            this.hasError = true;
            this.title = error.response.data.message;
            console.error("Bad Request:", error.response.data.message);
            // Thực hiện các thao tác cần thiết khi có lỗi 400
          } else {
            // Xử lý các trường hợp lỗi khác
            console.error("Server Error:", error.message);
            // Hiển thị thông báo lỗi chung cho người dùng
          }
        })
        .finally(() => (this.hasLoader = false));
    },
    deleteCartItem(item) {
      this.selectedItem = item;
      console.log(this.selectedItem);
      this.title = "Bạn có chắc muốn xóa mặt hàng này không?";
      this.hasQuestion = true;
    },
    close() {
      this.hasQuestion = false;
      this.hasError = false;
    },
    yes() {
      this.hasLoader = true;
      this.hasQuestion = false;

      this.$axios
        .delete(
          `${api.CartApi}/${this.selectedItem.cus_id}/${this.selectedItem.prod_id}`
        )
        .then(() => {
          this.title = "Xóa thành công";
          this.hasToast = true;
          setTimeout(() => {
            this.hasToast = false;
          }, 3000);
          this.getCart();
        })
        .finally(() => (this.hasLoader = false));
    },

    closeToast() {
      this.hasToast = false;
    },
  },
  watch: {
    getCustomer(value) {
      this.customer = value;
    },
  },
};
</script>
<style scoped>
.cart {
  border-radius: 8px;
  background-color: #fff;
  margin: 24px 48px;
  min-height: 400px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.cart-main {
  width: 1600px;
}
.cart-header {
  height: 60px;
  font-weight: 600;
  font-size: 20px;
  padding: 0 24px;
  line-height: 60px;
  color: #ef3073;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
}

.cart-content {
  padding: 24px;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 400px;
}
table {
  border: 1px solid #ccc;
  border-collapse: collapse;
}
table tr:nth-child(2n) {
  background: #eee;
}
table th {
  height: 40px;
  line-height: 40px;
  padding: 4px 12px;
  background: #ccc;
}
table img {
  width: 36px;
  height: 36px;
  border-radius: 100%;
}
table td {
  height: 40px;
  line-height: 40px;
  padding: 4px 12px;
  text-align: center;
}
table input {
  width: 100px;
  outline: none;
  border: 1px solid #333;
  background-color: #fff;
  color: #000;
  border-radius: 4px;
  padding: 2px;
}
table input:focus {
  border: 1px solid #3daa12;
}
.far {
  color: rgb(253, 63, 63);
  opacity: 0.8;
}
.far:hover {
  color: red;
  opacity: 1;
}

.cart-footer {
  display: flex;
  gap: 12px;
  justify-content: flex-end;
  padding: 12px 24px;
}

.cart-header-pay {
  display: flex;
  justify-content: space-between;
  padding: 12px 12px;
  height: 60px;
  font-weight: 600;
  font-size: 20px;
  padding: 0 24px;
  line-height: 60px;
  color: #ef3073;
  box-shadow: 0px 5px 4px #ccc;
}

.payment-form {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.3);
  display: flex;
  justify-content: center;
  align-items: center;
}
.payment-form-content {
  background: #fff;
  border-radius: 8px;
}
.form-content {
  padding: 12px;
  display: flex;
  justify-content: center;
  display: flex;
  flex-direction: column;
  gap: 12px;
}
.form-content-info {
  display: flex;
  justify-content: space-between;
}
.form-content .table {
  max-height: 200px;
  overflow: auto;
}
.address-item {
  display: flex;
  gap: 24px;
  /* justify-items: center; */
  margin: 12px 0;
}
.address-item i {
  width: 30px;
  line-height: 40px;
}
.address-item span {
  width: 180px;
  line-height: 40px;
}
.total-money {
  height: 40px;
  font-size: 20px;
  line-height: 40px;
  color: #3daa12;
  font-weight: 600;
}
</style>