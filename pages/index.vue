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
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from "vue";
import { fi_refresh } from "~/utils/svg.js";

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
