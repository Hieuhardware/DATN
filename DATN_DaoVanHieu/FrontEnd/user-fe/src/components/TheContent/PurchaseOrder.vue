<template>
  <div class="payment-form">
    <div class="payment-form-content">
      <div class="cart-header">
        <div>Đặt hàng</div>
      </div>
      <div class="form-content">
        <div class="table">
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
                v-for="(item, index) in items"
                :key="index + item.id + item.image"
              >
                <td style="min-width: 40px">{{ index + 1 }}</td>
                <td
                  style="
                    min-width: 40px;
                    display: flex;
                    justify-content: center;
                  "
                >
                  <img :src="item.image || avatar" alt="" />
                </td>
                <td style="min-width: 400px">{{ item.name }}</td>
                <td style="min-width: 100px">{{ priceFormat(item.price) }}</td>
                <td style="min-width: 120px">{{ item.unit }}</td>
                <td style="min-width: 110px">{{ item.quantity }}</td>
                <td style="min-width: 120px">
                  {{ priceFormat(item.price * item.quantity) }}
                </td>
              </tr>
            </tbody>
          </table>
          <div class="total-money">
            Tổng tiền thanh toán: <span>{{ priceFormat(totalMoney) }}VND</span>
          </div>
        </div>
        <div class="form-content-info">
          <div class="form-address">
            <div style="font-weight: 600">Thông tin nhận hàng</div>
            <div class="address-item">
              <i class="fas fa-user"></i>
              <span>Họ tên người nhận:</span>
              <input
                type="text"
                class="input"
                placeholder="Họ tên..."
                v-model="payMentInfo.name"
                :class="{ 'input-error': !isName.isTrue }"
                @change="ValidateOrder"
                :title="isName.title"
              />
            </div>
            <div class="address-item">
              <i class="fas fa-phone-alt"></i>
              <span>Số điện thoại:</span>
              <input
                type="text"
                class="input"
                placeholder="Số điện thoại..."
                v-model="payMentInfo.tel"
                :class="{ 'input-error': !isName.isTrue }"
                @change="ValidateOrder"
                :title="isTel.title"
              />
            </div>
          </div>
          <div class="form-note">
            <div class="address-item">
              <i class="fas fa-home"></i>
              <span>Địa chỉ nhận hàng:</span>
              <!-- <input
                type="text"
                class="input"
                placeholder="Địa chỉ..."
                v-model="payMentInfo.address"
                :class="{ 'input-error': !isAddress.isTrue }"
                @change="ValidateOrder"
                :title="isAddress.title"
              /> -->
              <input
                type="text"
                class="input"
                placeholder="Địa chỉ..."
                v-model="payMentInfo.address"
                :class="{ 'input-error': !isName.isTrue }"
                @change="ValidateOrder"
                :title="isAddress.title"
              />
            </div>
            <div class="address-item">
              <i class="fas fa-comment-dots"></i>
              <span>Ghi chú:</span>
              <input
                type="text"
                class="input"
                placeholder="Ghi chú..."
                v-model="payMentInfo.notes"
                :class="{ 'input-error': !isName.isTrue }"
                @change="ValidateOrder"
                :title="payMentInfo.notes"
              />
            </div>
          </div>
        </div>
      </div>
      <div class="cart-footer">
        <button class="button" @click="GetDefault()">Tự động điền</button>
        <button class="button" @click="AddOrder()">Tiến hàng đặt hàng</button>
      </div>
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
</template>
<script>
// import NoData from "@/components/Bases/NoData";//
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
    // NoData,
  },
  data() {
    return {
      totalMoney: 0,
      avatar: defaultAvatar,
      items: [],
      title: "",
      payMentInfo: {
        name: "",
        tel: "",
        address: "",
        notes: "",
      },
      isName: { title: "", isTrue: true },
      isTel: { title: "", isTrue: true },
      isAddress: { title: "", isTrue: true },
      hasLoader: false,
      hasQuestion: false,
      hasError: false,
      hasToast: false,
      customer: {},
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
    getCart() {
      this.hasLoader = false;
      this.$axios
        .get(`${api.CartApi}/${this.customer.id}`)
        .then((res) => {
          this.items = res.data;
          this.totalMoney = this.items.reduce(
            (total, item) => total + item.quantity * item.price,
            0
          );
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
    GetDefault() {
      let customer = this.getCustomer;
      console.log(customer);
      this.payMentInfo.name = customer.name;
      this.payMentInfo.tel = customer.tel;
      this.payMentInfo.address = customer.address;
    },
    ValidateOrder() {
      let check = 0;
      if (!this.payMentInfo.address) {
        this.isAddress.title = "Địa chỉ nhận hàng không được để trống.";
        this.isAddress.isTrue = false;
        this.title = "Địa chỉ nhận hàng không được để trống.";
        check = check + 1;
      } else {
        this.isAddress.title = "";
        this.isAddress.isTrue = true;
      }
      if (!this.payMentInfo.tel) {
        this.isTel.title = "Số điện thoại không được phép bỏ trống.";
        this.isTel.isTrue = false;
        this.title = "Số điện thoại không được phép bỏ trống.";
        check = check + 1;
      } else {
        this.isTel.title = "";
        this.isTel.isTrue = true;
      }
      if (this.payMentInfo.tel && !/^\d{10}$/.test(this.payMentInfo.tel)) {
        this.isTel.title = "Số diện thoại không đúng định dạng.";
        this.isTel.isTrue = false;
        this.title = "Số điện thoại không đúng định dạng.";
        check = check + 1;
      } else {
        this.isTel.title = "";
        this.isTel.isTrue = true;
      }
      if (!this.payMentInfo.name) {
        this.isName.title = "Họ tên không được phép bỏ trống.";
        this.isName.isTrue = false;
        this.title = "Họ tên không được phép bỏ trống.";
        check = check + 1;
      } else {
        this.isName.title = "";
        this.isName.isTrue = true;
      }
      return check;
    },
    async AddOrder() {
      if (this.ValidateOrder() > 0) {
        this.hasError = true;
      } else {
        var today = new Date();
        var date =
          today.getFullYear() +
          "-" +
          (today.getMonth() + 1) +
          "-" +
          today.getDate();
        let order = {
          id: 1,
          cus_id: this.getCustomer.id,
          order_date: date,
          status: "Đang chờ xác nhận",
          notes: this.payMentInfo.notes,
          address: this.payMentInfo.address,
          tel: this.payMentInfo.tel,
          name: this.payMentInfo.name,
        };
        let addedOrder;
        await this.$axios.post(api.OrderApi, order).then((res) => {
          addedOrder = res.status;
        });
        if (addedOrder == 201) {
          let orderId = await this.$axios
            .get(`${api.OrderApi}/newOrder`)
            .then((res) => res.data);
          let od = this.items.map((item) => ({
            prod_id: item.prod_id,
            order_id: orderId,
            quantity: item.quantity,
            price: item.price,
          }));
          this.$axios.post(api.OrderDetailApi, od).then((res) => {
            if (res.status == 201) {
              this.title = "Tạo đơn hàng thành công.";
              this.hasToast = true;
              setTimeout(() => {
                this.hasToast = false;
              }, 3000);
              this.deleteCard();
              this.isPayMent = false;
              this.$router.replace({ path: "/content" });
            }
          });
        }
      }
    },
    close() {
      this.hasQuestion = false;
      this.hasError = false;
    },
    yes() {
      this.hasLoader = true;
      this.hasQuestion = false;
    },
    deleteCard() {
      this.$axios.delete(`${api.CartApi}/${this.customer.id}`).then((res) => {
        if (res.status == 200) {
          this.$store.commit("CHANGE_CART", 0);
        }
      });
    },
    closeToast() {
      this.hasToast = false;
    },
    watch: {
      getCustomer(value) {
        this.customer = value;
      },
    },
  },
};
</script>

<style scoped>
.payment-form {
  display: flex;
  justify-content: center;
  align-items: center;
}
.payment-form-content {
  width: 1600px;
  border-radius: 8px;
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
.form-content {
  padding: 12px;
  display: flex;
  justify-content: center;
  align-content: center;

  gap: 12px;
}
.form-content-info {
  display: flex;
  flex-direction: column;
}
.form-content .table {
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
  font-weight: 600;
}

.total-money span {
  color: #ef3073;
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
</style>