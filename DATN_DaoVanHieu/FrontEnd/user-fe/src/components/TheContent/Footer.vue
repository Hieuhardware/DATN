<template>
  <div class="footer">
    <!-- <div class="footer__content">
      <img src="../../assets/image/logo.png" width="50" height="50" alt="" />
    </div> -->
    <!-- <div class="footer__content">
      <div class="icon-boder"><i class="fas fa-map-marker-alt"></i></div>
      <span
        >Số 298, đường Cầu Diễn, Phường Minh Khai, Quận Bắc Từ Liêm, Tp. Hà
        Nội</span
      >
    </div>
    <div class="footer__content">
      <div class="icon-boder"><i class="fas fa-phone-alt"></i></div>
      <span>0899999999</span>
    </div>
    <div class="footer__content">
      <div class="icon-boder"><i class="fas fa-envelope"></i></div>
      <span>vpp@gmail.com</span>
    </div> -->
    <div class="footer-main-icon">
      <div class="footer__content">
        <div class="icon-boder">
          <i class="fas fa-truck"></i>
        </div>
        <span>MIỄN PHÍ VẬN CHUYỂN</span>
      </div>
      <div class="footer__content">
        <div class="icon-boder"><i class="fas fa-medal"></i></div>
        <span>SẢN PHẨM CHẤT LƯỢNG</span>
      </div>
      <div class="footer__content">
        <div class="icon-boder"><i class="far fa-clock"></i></div>
        <span>ĐỔI TRẢ TRONG 10 NGÀY</span>
      </div>
      <div class="footer__content">
        <div class="icon-boder"><i class="fas fa-running"></i></div>
        <span>GIAO HÀNG SIÊU TỐC</span>
      </div>
      <div class="footer__content">
        <div class="icon-boder"><i class="far fa-file-alt"></i></div>
        <span>HOÁ ĐƠN TÀI CHÍNH</span>
      </div>
    </div>
  </div>
</template>
<style scoped>
.footer {
  display: flex;
  padding: 20px 48px;
  gap: 24px;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: #d8d7d7;
  width: 100%;
  border-top: 1px solid #ccc;
  z-index: 999;
}
.footer-main-icon {
  padding: 12px 0 0 0;
  width: 1600px;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.footer__content {
  display: flex;
  gap: 4px;
  align-items: center;
  color: #2c2a2a;
  flex-direction: column;
}
.icon-boder {
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 4px;
  color: #4f4b4b;
}
.footer__content span {
  font-size: 14px;
  font-weight: 600;
  padding-top: 12px;
}
.fas,
.far {
  color: #4f4b4b;
  font-size: 40px;
}
</style>