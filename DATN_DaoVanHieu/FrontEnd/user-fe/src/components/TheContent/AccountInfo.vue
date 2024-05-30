<template>
  <div class="account-info">
    <div class="account-info-main">
      <div class="account-info-header">Thông tin tài khoản</div>
      <div class="account-info-content">
        <div class="account-menu">
          <div @click="mainInfo">Thông tin cá nhân</div>
          <div @click="changePassword">Đổi mật khẩu</div>
          <div @click="myOrder">Đơn hàng của tôi</div>
        </div>
        <div class="account-option">
          <div v-if="isMainInfo">
            <div class="option-title">Thông tin tài khoản</div>
            <div>
              <div class="account-option-container">
                <div class="info-content--item">
                  <i class="fas fa-envelope"></i>
                  <span>Email:</span>
                  <input
                    type="text"
                    class="input"
                    v-model="customer.email"
                    disabled
                  />
                </div>
                <div class="info-content--item">
                  <i class="fas fa-user"></i>
                  <span>Họ tên:</span>
                  <input
                    type="text"
                    class="input"
                    v-model="customer.name"
                    :class="{ 'input-error': !isName.isTrue }"
                    :title="isName.title"
                  />
                </div>
                <div class="info-content--item">
                  <i class="fas fa-phone-alt"></i>
                  <span>Số điện thoại:</span>
                  <input
                    type="text"
                    class="input"
                    v-model="customer.tel"
                    :class="{ 'input-error': !isTel.isTrue }"
                    :title="isTel.title"
                  />
                </div>
                <div class="info-content--item">
                  <i class="fas fa-home"></i>
                  <span>Địa chỉ:</span>
                  <input type="text" class="input" v-model="customer.address" />
                </div>
              </div>
            </div>
            <div class="account-option-action">
              <button class="button button-secondary" @click="cancelChangeInfo">
                Hủy bỏ
              </button>
              <button class="button button-secondary" @click="renewInfo">
                Làm lại
              </button>
              <button class="button" @click="updateInfo">Cập nhật</button>
            </div>
          </div>

          <div v-if="isChangePassword">
            <div class="option-title">Đổi mật khẩu</div>
            <div>
              <div class="account-option-container">
                <div class="info-content--item">
                  <i class="fas fa-key"></i>
                  <span>Mật khẩu hiện tại:</span>
                  <input
                    type="text"
                    class="input"
                    placeholder="Mật khẩu hiện tại..."
                    autocomplete="false"
                    v-model="newPassword.oldPassword"
                  />
                </div>
                <div class="info-content--item">
                  <i class="fas fa-key"></i>
                  <span>Mật khẩu mới:</span>
                  <input
                    type="text"
                    class="input"
                    placeholder="Mật khẩu mới..."
                    v-model="newPassword.newPassword"
                  />
                </div>
                <div class="info-content--item">
                  <i class="fas fa-key"></i>
                  <span>Xác nhận mật khẩu:</span>
                  <input
                    type="text"
                    class="input"
                    placeholder="Xác nhận mật khẩu..."
                    v-model="newPassword.confirmPassword"
                  />
                </div>
              </div>
            </div>
            <div class="account-option-action">
              <button class="button button-secondary" @click="renewPassword">
                Làm lại
              </button>
              <button class="button" @click="updatePassword">Cập nhật</button>
            </div>
          </div>

          <div v-if="isMyOrder">
            <div class="option-title">Đơn hàng đã đặt</div>
            <div class="account-option-container">
              <table>
                <thead>
                  <tr>
                    <th>#</th>
                    <th>Ngày yêu cầu</th>
                    <th>Ghi chú</th>
                    <th>Tình trạng</th>
                    <th>Chi tiết</th>
                    <th>Thao tác</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(item, index) in orderList" :key="item.id + index">
                    <td>{{ index + 1 }}</td>
                    <td>{{ item.order_date }}</td>
                    <td>{{ item.notes }}</td>
                    <td>{{ item.status }}</td>
                    <td>
                      <span class="check" @click="myOrderDetail(item.id)"
                        ><i class="far fa-eye"></i
                      ></span>
                    </td>
                    <td>
                      <span class="delete" @click="deleteOrder(item)">Hủy</span>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>

          <div v-if="isMyOrderDetail">
            <div class="option-title">Chi tiết đơn hàng</div>
            <div class="account-option-container">
              <table>
                <thead>
                  <tr>
                    <th>#</th>
                    <th>Hình ảnh</th>
                    <th>Tên sản phẩm</th>
                    <th>Đơn giá</th>
                    <th>Đơn vị tính</th>
                    <th>Số lượng</th>
                    <th>Thành tiền</th>
                  </tr>
                </thead>
                <tbody>
                  <tr
                    v-for="(item, index) in productOrder"
                    :key="item.id + index"
                  >
                    <td>{{ index + 1 }}</td>
                    <td
                      style="
                        min-width: 40px;
                        display: flex;
                        justify-content: center;
                      "
                    >
                      <img :src="item.image || avatar" alt="" />
                    </td>
                    <td>{{ item.name }}</td>
                    <td>{{ priceFormat(item.price) }}</td>
                    <td>{{ item.unit }}</td>
                    <td>{{ item.quantity }}</td>
                    <td>{{ priceFormat(item.price * item.quantity) }}</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
    <ErrorPopup :title="title" @close="close" v-if="hasError"></ErrorPopup>
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
    <Loader v-if="hasLoader"></Loader>
  </div>
</template>
<script>
import ErrorPopup from "@/components/Bases/BasePopup/ErrorPopup";
import QuestionPopup from "@/components/Bases/BasePopup/QuestionPopup";
import ToastMesage from "@/components/Bases/ToastMesage";
import Loader from "@/components/Bases/Loader";
import api from "@/js/api";
export default {
  components: {
    ErrorPopup,
    QuestionPopup,
    ToastMesage,
    Loader,
  },
  data() {
    return {
      isMainInfo: true,
      isChangePassword: false,
      isMyOrder: false,
      isMyOrderDetail: false,
      customer: {},
      isPassword: { title: "", isTrue: true },
      isName: { title: "", isTrue: true },
      isTel: { title: "", isTrue: true },
      title: "",
      hasError: false,
      hasQuestion: false,
      hasToast: false,
      newPassword: {
        oldPassword: "",
        newPassword: "",
        confirmPassword: "",
      },
      hasLoader: false,
      orderList: [],
      productOrder: [],
      selectedOrder: "",
    };
  },
  created() {
    this.customer = this.getCustomer;
    if (!this.customer) {
      this.$router.replace({ path: "/content" });
    }
    this.getOrder();
  },
  computed: {
    getCustomer() {
      return this.$store.state.customer;
    },
  },
  methods: {
    mainInfo() {
      this.isMainInfo = true;
      this.isChangePassword = false;
      this.isMyOrder = false;
      this.isMyOrderDetail = false;
    },
    changePassword() {
      this.isMainInfo = false;
      this.isChangePassword = true;
      this.isMyOrder = false;
      this.isMyOrderDetail = false;
    },
    myOrder() {
      this.isMainInfo = false;
      this.isChangePassword = false;
      this.isMyOrder = true;
      this.isMyOrderDetail = false;
    },

    myOrderDetail(id) {
      this.isMainInfo = false;
      this.isChangePassword = false;
      this.isMyOrder = false;
      this.isMyOrderDetail = true;
      this.getOrderDetailView(id);
    },
    cancelChangeInfo() {
      this.customer = { ...this.getCustomer };
    },
    renewInfo() {
      this.customer.name = "";
      this.customer.tel = "";
      this.customer.address = "";
    },
    async getOrder() {
      await this.$axios
        .get(`${api.OrderApi}/${this.customer.id}`)
        .then((res) => {
          console.log(res.data);
          this.orderList = res.data.map((order) => {
            let date = order.order_date.slice(0, 10).split("/");
            return {
              id: order.id,
              order_date: `${date[1]}/${date[0]}/${date[2]}`,
              notes: order.notes,
              status: order.status,
            };
          });
        });
    },
    ////
    async getOrderDetailView(id) {
      await this.$axios.get(`${api.OrderDetailApi}/${id}`).then((res) => {
        console.log(res.data);
        this.productOrder = res.data.map((orderDetails) => {
          return {
            image: orderDetails.image,
            name: orderDetails.name,
            price: orderDetails.price,
            unit: orderDetails.unit,
            quantity: orderDetails.quantity,
          };
        });
      });
    },

    updateInfo() {
      let check = 0;
      if (!this.customer.name) {
        this.isName.title = "Họ tên không được phép bỏ trống.";
        this.isName.isTrue = false;
        this.title = "Họ tên không được phép bỏ trống.";
        check = check + 1;
      } else {
        this.isName.title = "";
        this.isName.isTrue = true;
      }
      if (this.customer.tel ? !/^\d{10}$/.test(this.customer.tel) : false) {
        this.isTel.title = "Số diện thoại không đúng định dạng.";
        this.isTel.isTrue = false;
        this.title = "Số điện thoại không đúng định dạng.";
        check = check + 1;
      } else {
        this.isTel.title = "";
        this.isTel.isTrue = true;
      }
      if (check === 0) {
        this.hasLoader = true;
        this.$axios
          .put(`${api.CustomerApi}/${this.customer.id}`, this.customer)
          .then((res) => {
            if (res.status === 200) {
              this.$store.commit("TOGGLE_CUSTOMER", this.customer);
              this.title = "Cập nhật thành công.";
              this.hasToast = true;
              setTimeout(() => {
                this.hasToast = false;
              }, 3000);
            }
          })
          .finally(() => (this.hasLoader = false));
      } else {
        this.hasError = true;
      }
    },
    renewPassword() {
      this.newPassword = {};
    },
    updatePassword() {
      if (this.newPassword.oldPassword === this.customer.password) {
        if (!this.newPassword.newPassword) {
          this.isPassword.title = "Mật khẩu mới không được phép bỏ trống.";
          this.isPassword.isTrue = false;
          this.title = "Mật khẩu mới không được phép bỏ trống.";
          this.hasError = true;
          return;
        }
        if (this.newPassword.newPassword === this.newPassword.confirmPassword) {
          this.customer.password = this.newPassword.newPassword;
          this.hasToast = true;
          this.$axios
            .put(`${api.CustomerApi}/${this.customer.id}`, this.customer)
            .then((res) => {
              if (res.status === 200) {
                this.$store.commit("TOGGLE_CUSTOMER", this.customer);
                this.title = "Cập nhật thành công.";
                this.hasToast = true;
                setTimeout(() => {
                  this.hasToast = false;
                }, 3000);
                this.newPassword = {};
              }
            })
            .finally(() => (this.hasLoader = false));
        } else {
          this.title = "Xác nhận mật khẩu không chính xác, vui lòng nhập lại.";
          this.hasError = true;
        }
      } else {
        this.title = "Mật khẩu hiện tại không đúng, vui lòng nhập chính xác.";
        this.hasError = true;
      }
    },
    deleteOrder(item) {
      if (item.status == "Đã giao hàng") {
        this.title = "Đơn hàng này đã giao, không thể hủy";
        this.hasError = true;
        return;
      }
      this.title = "Bạn có thật sự muốn hủy đơn hàng này không?";
      this.hasQuestion = true;
      this.selectedOrder = item.id;
    },
    close() {
      this.hasError = false;
      this.hasQuestion = false;
    },
    yes() {
      this.hasLoader = true;
      this.$axios
        .delete(`${api.OrderApi}/${this.selectedOrder}`)
        .then(() => {
          this.title = "Hủy đơn hàng thành công";
          this.hasQuestion = false;
          this.hasToast = true;
          setTimeout(() => {
            this.hasToast = false;
          }, 3000);
          this.getOrder();
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
    closeToast() {
      this.hasToast = false;
    },
  },
  watch: {
    getCustomer: function (value) {
      this.customer = value;
    },
  },
};
</script>
<style scoped>
.account-info {
  background-color: #fff;
  display: flex;
  justify-content: center;
}
.account-info-main {
  width: 1600px;
}
.account-info-header {
  height: 60px;
  font-weight: 600;
  font-size: 20px;
  padding: 0 24px;
  line-height: 60px;
  color: #ef3073;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
}
.account-info-content {
  display: flex;

  padding: 12px 48px;
  gap: 36px;
}
.account-menu {
  min-width: 300px;
}
.account-menu div {
  height: 40px;
  display: block;
  border-bottom: 1px solid #333;
  line-height: 40px;
  cursor: pointer;
}
.account-menu div:hover {
  color: #ef3073;
}
.account-option {
  flex-grow: 1;
}
.account-option-container {
  min-height: 300px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.option-title {
  height: 40px;
  text-align: center;
  line-height: 40px;
  font-size: 20px;
  font-weight: 600;
  color: #ef3073;
}
.info-content {
  display: flex;
  justify-content: center;
}
.info-content--item {
  display: flex;
  gap: 24px;
  justify-items: center;
  margin: 12px 0;
}
.info-content--item i {
  line-height: 40px;
}
.info-content--item span {
  width: 150px;
  line-height: 40px;
}
.info-content--item input {
  width: 400px;
}
.account-option-action {
  display: flex;
  gap: 24px;
  padding: 12px;
  justify-content: center;
}
table {
  border-collapse: collapse;
  margin: 12px;
}
table th {
  padding: 8px 12px;
  border: 1px solid #ccc;
}
table td {
  padding: 8px 12px;
  height: 40px;
  line-height: 40px;
  padding: 4px 12px;
  text-align: center;
}

tbody tr:nth-child(odd) {
  background-color: #ece8e8; /* Màu nền cho các dòng lẻ */
}

tbody tr:nth-child(even) {
  background-color: #ffffff; /* Màu nền cho các dòng chẵn */
}

tbody tr:hover {
  background-color: #eba0ba; /* Màu nền khi hover hoặc focus */
}

.check {
  font-size: 20px;
  line-height: 20px;
  font-weight: 600;
  color: #ccc;
  width: 100%;
  text-align: center;
  display: block;
  cursor: pointer;
}

.check:hover {
  color: #ef3073;
}
.delete {
  font-size: 20px;
  line-height: 20px;
  font-weight: 600;
  color: #ccc;
  width: 100%;
  text-align: center;
  display: block;
  cursor: pointer;
}
.delete:hover {
  color: red;
}
</style>