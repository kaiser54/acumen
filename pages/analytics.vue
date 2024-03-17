<template>
  <div class="page__wrapper">
    <div class="update flex">
      <p>Last updated {{ duration }} ago</p>
      <button class="btn flex" @click="fetchData()">
        <span v-html="fi_refresh" :class="{ refresh_loader: loading }"></span>
        <span>Refresh</span>
      </button>
    </div>
    <div class="dashcard__container flex">
      <CardWrapper v-for="data in dashboard" :key="data" class="dash__card__wrap">
        <div class="dash__card">
          <h3 class="header">{{ data.name }}</h3>
          <p class="details">
            <template v-if="data.name == 'Dashboard'">
              {{ bytesToGB(data.value) }} <sub>of 512GB</sub>
            </template>
            <template v-else>
              {{ data.value }}
            </template>
          </p>
        </div>
      </CardWrapper>
    </div>
    <div class="hr"></div>

    <div class="chart__container">
      <CardWrapper class="chart__wrapper">
        <header class="graph__header flex">
          <div class="graph__title">Daily Active Users</div>
          <div class="dropdown__group flex">
            <div class="dropdown__btn flex">
              30 Days
              <span v-html="arrow_down"></span>
            </div>
            <div class="dropdown__btn flex">
              All Activites
              <span v-html="arrow_down"></span>
            </div>
          </div>
        </header>
        <Chart :data="chartData" lineColor="#EA8D51" fillColor="#ea8d5126" />
      </CardWrapper>
      <CardWrapper class="chart__wrapper">
        <header class="graph__header flex">
          <div class="graph__title">Monthly Active Users</div>
          <div class="dropdown__group flex">
            <div class="dropdown__btn flex">
              All Activites
              <span v-html="arrow_down"></span>
            </div>
          </div>
        </header>
        <Chart :data="MonthData" lineColor="#15AE73" fillColor="#15ae7326" />
      </CardWrapper>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from "vue";
import { fi_refresh, arrow_down } from "~/utils/svg.js";

definePageMeta({
  layout: "dashboard",
});

const loading = ref(true);
const dataFetched = ref(false);
const duration = ref("");
let intervalId = null;

function getDuration() {
  let counter = 0;
  const interval = 1000;
  if (dataFetched.value) {
    intervalId = setInterval(() => {
      counter++;
      if (counter > 59) {
        duration.value = `${Math.floor(counter / 60)} minutes`;
      } else if (counter > 3600 - 1) {
        duration.value = `${Math.floor(counter / 3600)} hours`;
      } else {
        duration.value = `${Math.floor(counter)} seconds`;
      }
    }, interval);
    watch(
      () => dataFetched.value,
      (newValue) => {
        if (!newValue) clearInterval(intervalId);
      }
    );
  }
}

const dashboard = ref([]);

async function fetchData() {
  loading.value = true;
  clearInterval(intervalId);

  let data = null;

  try {
    const response = await fetch("https://sfe-m3if.onrender.com/api/v1/dashboard");
    data = await response.json();
    populateDashboard(data.data);
    dataFetched.value = true;
    getDuration();
  } catch (error) {
    console.error("Error fetching data:", error);
  } finally {
    loading.value = false;
  }
}

function populateDashboard(data) {
  if (data) {
    dashboard.value = [
      { name: "Users Online", value: data.usersOnline },
      { name: "Users Active Today", value: data.usersActiveToday },
      { name: "Users Active This Month", value: data.usersActiveThisMonth },
      { name: "Peak Concurrency", value: data.peakConcurrency },
      { name: "Dashboard", value: data.dashboard },
    ];
  }
}

function bytesToGB(bytes) {
  const bytesInGB = 1024 ** 3;
  return Math.ceil(bytes / bytesInGB);
}

const chartData = [
  { day: "MON", count: 10 },
  { day: "TUE", count: 20 },
  { day: "WED", count: 15 },
  { day: "THU", count: 25 },
  { day: "FRI", count: 22 },
  { day: "SAT", count: 30 },
  { day: "SUN", count: 28 },
];
const MonthData = [
  { month: "JAN", count: 10 },
  { month: "FEB", count: 26 },
  { month: "MAR", count: 15 },
  { month: "APR", count: 25 },
  { month: "MAY", count: 22 },
  { month: "JUN", count: 30 },
  { month: "JUL", count: 8 },
  { month: "AUG", count: 18 },
  { month: "SEP", count: 5 },
  { month: "OCT", count: 27 },
  { month: "NOV", count: 15 },
  { month: "DEC", count: 30 },
];

onMounted(() => {
  fetchData();
});
</script>

<style scoped>
button.btn {
  background: none;
  align-items: center;
  gap: 9px;
  color: #15ae73;
}

.loading {
  animation: skeleton__loader 1s ease-in-out infinite;
}

sub {
  vertical-align: baseline;
  font-size: 15px;
}

.loading::after {
  content: "";
  display: block;
}

.update {
  align-items: center;
  gap: 17px;
  margin-bottom: 8px;
}

.hr {
  margin-block: 24px;
}

.chart__container {
  display: flex;
  gap: 23px;
}

.chart__wrapper {
  width: fit-content;
  padding-inline: 34px;
  padding-bottom: 17px;
  padding-top: 10px;
}

.graph__header {
  justify-content: space-between;
  align-items: center;
  width: 100%;
  margin-bottom: 46px;
}

.graph__title {
  font-size: 12px;
  font-weight: 400;
  line-height: 24px;
  color: #566ba0;
  text-transform: uppercase;
}

.dropdown__group {
  align-items: center;
  gap: 8px;
}

.dropdown__btn {
  color: #021c3e;
  font-size: 14px;
  font-weight: 400;
  letter-spacing: 0.6%;
  line-height: 20px;
  cursor: pointer;
  align-items: center;
  justify-content: center;
  gap: 8px;
  height: 40px;
  padding: 8px 16px;
  background: #ededed;
  border-radius: 4px;
}

@keyframes skeleton__loader {
  0% {
    opacity: 0.5;
  }

  50% {
    opacity: 1;
  }

  100% {
    opacity: 0.5;
  }
}
</style>

<style>
.refresh_loader svg {
  animation: rotate 0.7s linear infinite;
}

@keyframes rotate {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
import type { color } from "chart.js/helpers"; import type { color } from
"chart.js/helpers";
