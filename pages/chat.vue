<template>
  <div class="page__wrapper">
    <div class="dashcard__container flex">
      <CardWrapper v-for="data in dashboard" :key="data" class="dash__card__wrap">
        <div class="dash__card">
          <h3 class="header">{{ data.name }}</h3>
          <p class="details">
            <template v-if="data.id == 3">
              {{ bucketSize(data.value) }}
            </template>
            <template v-else>
              {{ data.value }}
            </template>
          </p>
        </div>
      </CardWrapper>
    </div>
    <div class="table_tab_header flex">
      <div class="tab_switch">
        <div
          class="tab_btn"
          :class="{ active: activeTab === 1 }"
          @click="setActiveTab(1)"
        >
          Direct Messages
          <span class="indicator"></span>
        </div>
        <div
          class="tab_btn"
          :class="{ active: activeTab === 2 }"
          @click="setActiveTab(2)"
        >
          Groups
          <span class="indicator"></span>
        </div>
      </div>
      <div class="input__filter flex">
        <div class="input">
          <input
            type="text"
            name=""
            id=""
            placeholder="Search..."
            v-model="searchTerm"
            @input="handleSearch()"
          />
          <span v-html="search"></span>
        </div>
        <button class="btn filter" v-html="filter" @click="handleCloseFilter()"></button>
      </div>
    </div>
    <div class="hr"></div>
    <CardWrapper>
      <table class="table-sortable">
        <thead>
          <tr>
            <th
              v-for="(head, index) in tableHeader"
              :key="head.id"
              @click="sortTable(head.id)"
              :class="{
                'th-sort-asc': !ascending,
              }"
            >
              <span class="sort">
                <span> {{ head.name }}</span>
                <span v-html="sort" class="svg"> </span>
              </span>
            </th>
            <th></th>
          </tr>
        </thead>
        <tbody v-if="!loading">
          <tr v-for="(row, index) in sortedRows" :key="index">
            <td>{{ row.full_name }}</td>
            <td>{{ row.message_sent }}</td>
            <td>{{ bucketSize(row.media_storage_used) }}</td>
            <td>{{ formatDate(row.date_created) }}</td>
            <td>{{ row.media_sent }}</td>
            <td class="ellipsis">
              <span v-html="u_ellipsis" @click="showPopUp({ row, index })"></span>
              <CardWrapper class="pop__up" v-if="index == activeIndex">
                <div class="view pop flex">
                  <p>View</p>
                  <span v-html="eye"></span>
                </div>
                <div class="disable pop flex">
                  <p>Disable</p>
                  <span v-html="close_circle"></span>
                </div>
              </CardWrapper>
            </td>
          </tr>
        </tbody>
      </table>
      <p v-if="loading" style="text-align: center; margin-top: 34px">
        Please wait, loading...
      </p>
      <div class="pagination">
        <button
          class="btn prev"
          :disabled="!tableData.hasPrevPage"
          @click="fetchMessages(tableData.prevPage)"
        >
          <span v-html="carat"></span>
        </button>
        <button
          class="btn prev"
          v-for="num in tableData.totalPages"
          @click="fetchMessages(num)"
          :class="{ active: num == tableData.page }"
        >
          {{ num }}
        </button>
        <button
          class="btn next"
          :class="{ disabled: !tableData.hasNextPage }"
          @click="fetchMessages(tableData.nextPage)"
          :disabled="!tableData.hasNextPage"
        >
          <span v-html="carat"></span>
        </button>
      </div>
    </CardWrapper>

    <Filter
      :filterCapsule
      :activeFilterCapsule
      @filterCapsule="handleFilterCapsule($event)"
      @handleCloseFilter="handleCloseFilter()"
      v-if="showFilter"
    />
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import {
  u_ellipsis,
  search,
  sort,
  close_circle,
  eye,
  clear,
  carat,
  filter,
} from "~/utils/svg.js";

definePageMeta({
  layout: "dashboard",
});

const loading = ref(true);
async function fetchData() {
  loading.value = true;
  let data = null;
  try {
    const response = await fetch("https://sfe-m3if.onrender.com/api/v1/chat_dashboard");
    data = await response.json();
    populateDashboard(data.data);
  } catch (error) {
    console.error("Error fetching data:", error);
  } finally {
    loading.value = false;
  }
}

const pageLimit = ref(5);
const tableData = ref([]);
async function fetchMessages(page) {
  loading.value = true;
  let data = null;
  try {
    const response = await fetch(
      `https://sfe-m3if.onrender.com/api/v1/messages?page=${page}&limit=${pageLimit.value}`
    );
    data = await response.json();
    tableData.value = data;
    sortedRows.value = data.data;
  } catch (error) {
    console.error("Error fetching data:", error);
  } finally {
    loading.value = false;
  }
}

const dashboard = ref([]);
function populateDashboard(data) {
  if (data) {
    dashboard.value = [
      { id: 1, name: "No of Users", value: data.numberOfUsers },
      { id: 2, name: "Total Message Sent", value: data.totalMessagesSent },
      { id: 3, name: "Total Media Storage Used", value: data.totalStorageUsed },
      { id: 4, name: "Total Media Sent", value: data.totalMediaSent },
    ];
  }
}

function bucketSize(size) {
  const units = ["B", "KB", "MB", "GB", "TB"];
  let i = 0;
  while (size >= 1024 && i < units.length - 1) {
    size /= 1024;
    i++;
  }
  return size.toFixed(1) + " " + units[i];
}

const tableHeader = [
  {
    id: 0,
    name: "Users",
  },
  {
    id: 1,
    name: "Messages Sent",
  },
  {
    id: 2,
    name: "Media storage Used",
  },
  {
    id: 3,
    name: "Date Created",
  },
  {
    id: 4,
    name: "Media Sent",
  },
];

const sortedColumnIndex = ref(0);
const ascending = ref(true);
const sortedRows = ref([]);

function sortTable(column) {
  ascending.value = sortedColumnIndex.value === column ? !ascending.value : true;
  sortedColumnIndex.value = column;
  sortedRows.value = sortRows(column);
}

function sortRows(column) {
  const dirModifier = ascending.value ? 1 : -1;
  return tableData.value.data.sort((a, b) => {
    const aColText = a[Object.keys(a)[column]].toString().trim();
    const bColText = b[Object.keys(b)[column]].toString().trim();
    return aColText.localeCompare(bColText) * dirModifier;
  });
}

const searchTerm = ref("");
function handleSearch() {
  sortedRows.value = tableData.value.data.filter((row) => {
    return Object.values(row).some((value) => {
      return value.toString().toLowerCase().includes(searchTerm.value.toLowerCase());
    });
  });
}

const activeIndex = ref(null);
function showPopUp(e) {
  if (activeIndex.value == e.index) {
    activeIndex.value = null;
  } else {
    activeIndex.value = e.index;
    console.log(e.row);
  }
}

function formatDate(dateString) {
  const datePart = dateString.match(/\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.\d{3}Z/g)[0];
  const date = new Date(datePart);

  const day = date.getDate();
  const monthNames = [
    "Jan",
    "Feb",
    "Mar",
    "Apr",
    "May",
    "Jun",
    "Jul",
    "Aug",
    "Sep",
    "Oct",
    "Nov",
    "Dec",
  ];
  const month = monthNames[date.getMonth()];
  const year = date.getFullYear();

  const formattedDate = `${day} ${month}, ${year}`;

  return formattedDate;
}

const activeTab = ref(1);
function setActiveTab(tabName) {
  activeTab.value = tabName;
}

const activeFilterCapsule = ref(0);
const filterCapsule = ref(["Today", "Last 7 days", "This month", "Last 3 months"]);
function handleFilterCapsule(e) {
  activeFilterCapsule.value = e;
}

const showFilter = ref(false);
function handleCloseFilter() {
  showFilter.value = !showFilter.value;
}

onMounted(async () => {
  await fetchData();
  await fetchMessages(1);
  sortTable(0);
});
</script>

<style scoped>
.dashcard__container {
  justify-content: space-between;
}
.table_tab_header {
  width: 100%;
  justify-content: space-between;
  align-items: center;
  margin-top: 46px;
}

.tab_switch {
  display: flex;
  gap: 57px;
}

.tab_btn {
  position: relative;
  font-size: 16px;
  color: #5a483c;
  cursor: pointer;
  transition: all 0.3s ease-in;
}

.tab_btn.active {
  color: #ea8d51;
}

.tab_btn .indicator {
  position: absolute;
  top: 42px;
  display: block;
  height: 2px;
  width: 0%;
  transition: all 0.3s ease-in;
}

.tab_btn.active .indicator {
  background: #ea8d51;
  width: 100%;
}

.hr {
  margin-bottom: 20px;
}

.input__filter {
  align-items: center;
  gap: 12px;
}

.input {
  width: 264px;
  background: #fff;
  padding-right: 17px;
  margin-bottom: 12px;
}

.filter {
  padding: 12px;
  border-radius: 4px;
  margin-bottom: 12px;
  background: #fff;
  border: 1px solid #e7e9ff;
  border-radius: 4px;
}

.disable {
  color: #d53120;
}

table {
  border-collapse: collapse;
  width: 100%;
}

table tr {
  height: 58px;
  text-align: left;
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
  padding: 0.35em;
}

.sort {
  display: flex;
  align-items: center;
  gap: 5px;
}

table tr th {
  font-weight: 400;
  font-size: 14px;
  line-height: 24px;
  color: #9999bc;
  text-transform: uppercase;
}

table tr td {
  font-weight: 400;
  font-size: 16px;
  line-height: 24px;
  color: #071439;
  text-transform: capitalize;
}

table tr th:first-child,
table tr td:first-child {
  padding-left: 55px;
}

table tr th:last-child,
table tr td:last-child {
  padding-right: 55px;
}

.table-sortable th {
  cursor: pointer;
}

.ellipsis {
  position: relative;
}

.pop__up {
  position: absolute;
  width: 197px;
  /* height: 88px; */
  right: 75%;
  z-index: 9;
}

.view.pop {
  border: 1px solid rgba(0, 0, 0, 0.1);
}

.pop {
  cursor: pointer;
  align-items: center;
  justify-content: space-between;
  padding: 12px 20px 12px 16px;
}

.pagination {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  margin-block: 34px;
}

.pagination button {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  background: #fff;
  border: 1px solid #e7e9ff;
  border-radius: 4px;
  color: #212b36;
  font-weight: bold;
}

.pagination button:disabled {
  cursor: not-allowed;
  background: #919eab;
}

.pagination button.active {
  color: #0546e0;
  border-color: #0546e0;
}
</style>

<style>
button.next svg,
.th-sort-asc .sort .svg {
  rotate: 180deg;
}

.filter svg path {
  fill: #212b36;
}
</style>
