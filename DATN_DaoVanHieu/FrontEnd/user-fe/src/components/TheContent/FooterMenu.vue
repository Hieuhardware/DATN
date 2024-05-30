<template>
  <div class="footer-main-menu">
    <div class="menu">
      <div class="box-menu">
        <h4>GIỚI THIỆU</h4>
        <ul>
          <li>Về chúng tôi</li>
          <li>Cộng đồng</li>
          <li>Đối tác khách hàng</li>
          <li>Câu hỏi thường gặp</li>
        </ul>
      </div>
      <div class="box-menu">
        <h4>CHÍNH SÁCH</h4>
        <ul>
          <li>Giao hàng miễn phí</li>
          <li>Phương thức thanh toán</li>
          <li>Chính sách đổi trả</li>
          <li>Chính sách bảo mật</li>
        </ul>
      </div>
      <div class="box-menu">
        <h4>CHƯƠNG TRÌNH ƯU ĐÃI</h4>
        <ul>
          <li>Chính sách đại lý</li>
          <li>Khuyễn mãi mã giấy in</li>
          <li>Chiết khấu mua hàng</li>
          <li>Ưu đãi cho khách hàng</li>
        </ul>
      </div>
      <div class="box-menu">
        <h4>CÔNG TY VPP</h4>
        <ul>
          <li>
            <div class="icon"><i class="fas fa-map-marker-alt"></i></div>
            Số 298, đường Cầu Diễn, Phường Minh Khai, Quận Bắc Từ Liêm, Tp. Hà
            Nội
          </li>
          <li>
            <div class="icon"><i class="fas fa-phone-alt"></i></div>
            0979888888
          </li>
          <li>
            <div class="icon"><i class="far fa-envelope"></i></div>
            daovanhieu2910@gmail.com
          </li>
          <li>
            <div class="icon"><i class="fas fa-wifi"></i></div>
            https://localhost
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>
<style scoped>
.footer-main-menu {
  display: flex;
  justify-content: center;
  width: 100%;
  background-color: #fff;
  box-shadow: 0px -5px 15px rgba(0, 0, 0, 0.3);
}
.menu {
  padding: 40px 0 0 0;
  width: 1600px;
  display: flex;
  flex-direction: row;
  gap: 100px;
}

.box-menu {
  margin-right: 50px;
}

.box-menu h4 {
  color: #ef3073;
  font-weight: 700;
}

.box-menu ul li {
  transition: transform 0.3s ease;
  font-size: 13px;
  line-height: 30px;
  border-bottom: 1px solid #b3b1b1;
}
.box-menu ul li:hover {
  color: #ef3073;
  cursor: pointer;
  transform: translateX(5px);
}
li .icon {
  display: inline-block;
  padding-right: 12px;
  color: #ef3073;
}
</style>
