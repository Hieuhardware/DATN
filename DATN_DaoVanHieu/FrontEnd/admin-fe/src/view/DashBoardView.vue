<template>
  <div>
    <div class="view">
      <div class="view-title" data-aos="fade-left" data-aos-duration="1500">
        Trang chủ
      </div>
      <div class="card">
        <div class="card-item">
          <div class="card-item-header">
            <div class="card-itm-header-title">Thống kê chung</div>
          </div>
          <div class="card-item-content">
            <div class="report-container">
              <div class="report report-1">
                <div class="report-icon">
                  <i class="fas fa-cart-plus"></i>
                </div>
                <div class="report-wrap">
                  <div class="report-title">Đơn hàng trong tháng:</div>
                  <div class="report-number">
                    {{ dataTotal.order }}
                  </div>
                </div>
                <div class="report-detail">
                  <router-link to="/order" class="navbar-item">
                    <i class="fas fa-arrow-alt-circle-right"></i>
                  </router-link>
                </div>
              </div>
              <div class="report report-2">
                <div class="report-icon">
                  <i class="fas fa-book"></i>
                </div>
                <div class="report-wrap">
                  <div class="report-title">Số lượng sản phẩm:</div>
                  <div class="report-number">{{ dataTotal.product }}</div>
                </div>
                <div class="report-detail">
                  <router-link to="/product" class="navbar-item">
                    <i class="fas fa-arrow-alt-circle-right"></i>
                  </router-link>
                </div>
              </div>
              <div class="report report-3">
                <div class="report-icon">
                  <i class="fas fa-users"></i>
                </div>
                <div class="report-wrap">
                  <div class="report-title">Khách hàng mới trong tháng:</div>
                  <div class="report-number">
                    {{ dataTotal.customer }}
                  </div>
                </div>
                <div class="report-detail">
                  <router-link to="/customer" class="navbar-item">
                    <i class="fas fa-arrow-alt-circle-right"></i>
                  </router-link>
                </div>
              </div>
              <div class="report report-4">
                <div class="report-icon">
                  <i class="fas fa-coins"></i>
                </div>
                <div class="report-wrap">
                  <div class="report-title">Doanh thu tháng gần đây:</div>
                  <div class="report-number">
                    {{ priceFormat(dataTotal.money) }}VND
                  </div>
                </div>
                <div class="report-detail">
                  <router-link to="/order" class="navbar-item">
                    <i class="fas fa-arrow-alt-circle-right"></i>
                  </router-link>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="card-item">
          <div class="card-item-header">
            <div class="card-itm-header-title">Biểu đồ thống kê chi tiết</div>
            <div class="time-button">
              <button class="button" @click="renderMonthReport()">
                Báo cáo theo tháng
              </button>
              <button class="button" @click="renderYearReport()">
                Báo cáo theo năm
              </button>
            </div>
          </div>
          <div class="card-item-content">
            <div class="time-filter">
              <div class="time">
                {{ mainTitle }}
              </div>
              <div class="time-container">
                <div class="time">
                  <md-field>
                    <label>Tháng</label>
                    <md-select
                      v-model="timeFilter.month"
                      @md-selected="
                        () => {
                          renderMonthReport();
                          renderMonthReportPie();
                        }
                      "
                    >
                      <md-option
                        :value="item.id"
                        v-for="(item, index) in monthNumber"
                        :key="index"
                      >
                        {{ item.name }}
                      </md-option>
                    </md-select>
                  </md-field>
                </div>
                <div class="time">
                  <md-field>
                    <label>Năm</label>
                    <md-select
                      v-model="timeFilter.year"
                      @md-selected="
                        () => {
                          renderYearReport();
                          renderYearReportPie();
                        }
                      "
                    >
                      <md-option
                        :value="item.id"
                        v-for="(item, index) in yearNumber"
                        :key="index"
                      >
                        {{ item.name }}
                      </md-option>
                    </md-select>
                  </md-field>
                </div>
              </div>
            </div>
            <div class="chart-view">
              <div class="chart-view-item">
                <Chart
                  :chartData="chartData"
                  :chartOptions="options"
                  :hasChart="hasChart"
                ></Chart>
                <div class="chart-title">
                  <span>Đồ thị tình hình bán hàng</span>
                </div>
              </div>
              <div class="chart-view-item">
                <ChartPie
                  :chartData="chartData2"
                  :chartOptions="options2"
                  :hasChart="hasChart"
                >
                </ChartPie>
                <div class="chart-title">
                  <span>Đồ thị số lượng sản phẩm bán nhiều</span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <Loader v-if="hasLoader"></Loader>
    <ErrorPopup @close="close" :title="title" v-if="hasError"></ErrorPopup>
  </div>
</template>
<script>
import Loader from "@/components/Bases/Loader";
import ErrorPopup from "@/components/Bases/BasePopup/ErrorPopup";
import Chart from "@/view/Chart";
import ChartPie from "@/view/ChartPie";
import api from "@/js/api";
export default {
  components: {
    Loader,
    ErrorPopup,
    Chart,
    ChartPie,
  },
  data() {
    return {
      mainTitle: "",
      dataTotal: {},
      hasLoader: false,
      hasError: false,
      monthNumber: [],
      yearNumber: [],
      monthList: [
        "Tháng 1",
        "Tháng 2",
        "Tháng 3",
        "Tháng 4",
        "Tháng 5",
        "Tháng 6",
        "Tháng 7",
        "Tháng 8",
        "Tháng 9",
        "Tháng 10",
        "Tháng 11",
        "Tháng 12",
      ],
      timeFilter: {
        month: "",
        year: "",
      },
      chartData: {
        labels: [],
        datasets: [
          {
            label: "Số đơn hàng",
            backgroundColor: ["#3cba9f"],
            data: [63.2, 52.1, 73.4, 78.4, 80.5, 79.2, 82.1],
          },
          {
            label: "Giá trị (Triệu VND)",
            backgroundColor: ["#8e5ea2"],
            data: [63.2, 52.1, 73.4, 78.4, 80.5, 79.2, 82.1],
          },
        ],
      },
      chartData2: {
        labels: [],
        datasets: [
          {
            backgroundColor: [
              "#41B883",
              "#E46651",
              "#00D8FF",
              "#DD1B16",
              "#41B883",
            ],
            data: [],
          },
        ],
      },
      options: {
        title: {
          display: true,
          text: "Đồ thị tình hình bán hàng theo thời gian",
        },
        responsive: true,
        aspectRatio: false,
      },
      options2: {
        responsive: true,
        maintainAspectRatio: false,
      },
      hasChart: false,
    };
  },
  created() {
    let date = new Date();
    let nowMonth = date.getMonth() + 1;
    for (let i = 1; i < 13; i++) {
      this.monthNumber.push({ id: i, name: `Tháng ${i}` });
    }
    this.timeFilter.month = nowMonth;
    let nowYear = date.getFullYear();
    for (let i = nowYear; i > 2000; i--) {
      this.yearNumber.push({ id: i, name: i });
    }
    this.timeFilter.year = nowYear;
    this.getDataTotal();
    this.renderMonthReportPie();
    this.renderMonthReport();
  },
  methods: {
    getDataTotal() {
      this.hasLoader = true;
      this.$axios
        .get(`${api.AdminApi}/datatotal`, {
          params: {
            month: this.timeFilter.month,
            year: this.timeFilter.year,
          },
        })
        .then((res) => {
          this.dataTotal = res.data;
          console.log(this.dataTotal);
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
    close() {
      this.hasError = false;
    },
    async renderMonthReport() {
      this.hasChart = false;
      let numberDate = this.daysInMonth(
        this.timeFilter.month,
        this.timeFilter.year
      );
      let dates = [];
      for (let index = 1; index <= numberDate; index++) {
        dates.push(index);
      }
      this.hasLoader = true;
      await this.$axios
        .get(`${api.AdminApi}/datachart/month`, {
          params: {
            day: numberDate,
            month: this.timeFilter.month,
            year: this.timeFilter.year,
          },
        })
        .then((res) => {
          console.log(res.data);
          this.chartData.datasets[0].data = res.data.order;
          console.log();
          this.chartData.datasets[1].data = res.data.money.map(
            (item) => item / 1000000
          );
        })
        .finally(() => (this.hasLoader = false));
      this.chartData.labels = dates;
      this.hasLoader = true;
      setTimeout(() => {
        this.hasChart = true;
        this.mainTitle = `Biểu đồ thống kê theo tháng ${this.timeFilter.month} năm ${this.timeFilter.year}`;
        this.hasLoader = false;
      }, 500);
    },
    async renderMonthReportPie() {
      this.hasChart = false;
      this.hasLoader = true;
      await this.$axios
        .get(`${api.AdminApi}/datachartpie/month`, {
          params: {
            month: this.timeFilter.month,
            year: this.timeFilter.year,
          },
        })
        .then((res) => {
          console.log(res.data);
          this.chartData2.datasets[0].data = [];
          this.chartData2.labels = [];
          for (let index = 0; index < res.data.length; index++) {
            this.chartData2.datasets[0].data.push(
              res.data[index].total_quantity_sold
            );
            this.chartData2.labels.push(res.data[index].name);
          }
        })
        .finally(() => (this.hasLoader = false));

      this.hasLoader = true;
      setTimeout(() => {
        this.hasChart = true;
        this.hasLoader = false;
      }, 1000);
    },
    async renderYearReportPie() {
      this.hasChart = false;
      this.hasLoader = true;
      await this.$axios
        .get(`${api.AdminApi}/datachartpie/year`, {
          params: {
            year: this.timeFilter.year,
          },
        })
        .then((res) => {
          console.log(res.data);
          this.chartData2.datasets[0].data = [];
          this.chartData2.labels = [];
          for (let index = 0; index < res.data.length; index++) {
            this.chartData2.datasets[0].data.push(
              res.data[index].total_quantity_sold
            );
            this.chartData2.labels.push(res.data[index].name);
          }
        })
        .finally(() => (this.hasLoader = false));

      this.hasLoader = true;
      setTimeout(() => {
        this.hasChart = true;
        this.hasLoader = false;
      }, 1000);
    },
    async renderYearReport() {
      this.hasChart = false;
      this.hasLoader = true;
      await this.$axios
        .get(`${api.AdminApi}/datachart/year`, {
          params: {
            year: this.timeFilter.year,
          },
        })
        .then((res) => {
          this.chartData.datasets[0].data = res.data.order;
          this.chartData.datasets[1].data = res.data.money.map(
            (item) => item / 1000000
          );
        })
        .finally(() => (this.hasLoader = false));
      this.chartData.labels = this.monthList;
      this.hasLoader = true;
      setTimeout(() => {
        this.hasChart = true;
        this.mainTitle = `Biểu đồ thống kê theo năm ${this.timeFilter.year}`;
        this.hasLoader = false;
      }, 500);
    },

    daysInMonth(month, year) {
      return new Date(year, month, 0).getDate();
    },
  },
};
</script>
<style scoped>
.view {
  width: 100%;
  padding: 12px;
  overflow: hidden;
}
.card {
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: 12px;
}
.view-title {
  font-size: 30px;
  font-weight: 600;
  height: 40px;
  line-height: 40px;
  padding: 0 12px;
}
.card-item {
  background-color: #fff;
  border-radius: 8px;
  padding: 12px;
  box-shadow: 0px 1px 2px rgba(0, 0, 0, 0.1), 0px 1px 9px rgba(0, 0, 0, 0.06),
    0px 3px 5px rgba(0, 0, 0, 0.07);
}
.card-item-header {
  display: flex;
  justify-content: space-between;
  border-bottom: 2px solid #ccc;
  margin-bottom: 8px;
  padding: 0 0 6px 0;
}
.card-itm-header-title {
  height: 40px;
  line-height: 40px;
  font-size: 22px;
}
.card-item-content {
  display: flex;
  gap: 12px;
  flex-wrap: wrap;
}
.report-container {
  display: flex;
  width: 100%;
  justify-content: center;
  align-items: center;
  gap: 12px;
}
.report {
  flex-basis: 25%;
  min-height: 100px;
  display: flex;
  justify-content: space-between;
  flex-direction: row;
  align-items: center;
  border-radius: 8px;
  padding-left: 16px;
}

.report-1 {
  border-left: 5px solid #6610f2;
  padding: 10px;
  margin: 20px 0;
  background-color: #f9f9f9;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  border-radius: 4px;
}
.report-2 {
  border-left: 5px solid rgb(13, 233, 0);
  padding: 10px;
  margin: 20px 0;
  background-color: #f9f9f9;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  border-radius: 4px;
}
.report-3 {
  border-left: 5px solid rgb(237, 38, 16);
  padding: 10px;
  margin: 20px 0;
  background-color: #f9f9f9;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  border-radius: 4px;
}
.report-4 {
  border-left: 5px solid rgb(16, 151, 237);
  padding: 10px;
  margin: 20px 0;
  background-color: #f9f9f9;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  border-radius: 4px;
}
.report-detail {
  align-self: flex-start;
}
.report-wrap {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}
.report i {
  font-size: 24px;
  color: #045ebe;
}
.report .report-icon i {
  font-size: 36px;
}
.report .report-title {
  text-align: center;
  font-size: 20px;
  color: #045ebe;
  line-height: 26px;
}
.report .report-number {
  text-align: center;
  font-size: 24px;
  color: #045ebe;
}
.choice {
  display: flex;
  gap: 12px;
}
.time-filter {
  display: flex;
  gap: 5%;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  padding-right: 5%;
  color: #045ebe;
  font-size: 24px;
}
.time-container {
  display: flex;
  gap: 12px;
}
.time-button {
  display: flex;
  gap: 12px;
}

.chart-view {
  display: flex;
}
.chart-view-item {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.chart-title {
  margin-top: 20px;
}

.chart-title span {
  font-size: 20px;
  font-weight: 400;
}
</style>