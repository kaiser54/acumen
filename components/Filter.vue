<template>
  <div class="table_filter_wrap">
    <div class="table__filter">
      <div class="table__header">
        <h3>Filter Table</h3>
        <span v-html="clear" class="clear flex" @click="close()"></span>
      </div>
      <div class="filter__wrap__group__cta">
        <div class="wrap">
          <div class="filter__wrap flex">
            <div
              class="filter__capsule flex"
              :class="{ active: index == activeFilterCapsule }"
              v-for="(data, index) in filterCapsule"
              :key="data"
              @click="handleFilterCapsule({ data, index })"
            >
              {{ data }}
            </div>
          </div>
          <div class="select__filter flex">
            <div class="filter__field">
              <label for="">Date From</label>
              <input class="input" type="date" name="" id="" v-model="minDate"/>
            </div>
            <div class="filter__field">
              <label for="">Date To</label>
              <input class="input" type="date" name="" id="" v-model="maxDate"/>
            </div>
          </div>
          <div class="select__filter flex">
            <div class="filter__field">
              <label for="">Messages</label>
              <div class="select input">
                <select name="" id="" v-model="selectedMessageRange">
                  <option value="" disabled selected>Please select</option>
                  <option value="0-500">0 - 500 Messages</option>
                  <option value="500-2000">500 - 2000 messages</option>
                  <option value="2000+">2000+ Messages</option>
                </select>
              </div>
            </div>
          </div>
          <div class="select__filter flex">
            <div class="filter__field">
              <label for="">Media Storage Used</label>
              <div class="select input">
                <select name="" id="" v-model="selectedMediaRange">
                  <option value="" disabled selected>Please select</option>
                  <option value="0-51200">0 - 50 MB</option>
                  <option value="51201-204800">50 MB - 200MB</option>
                  <option value="204801-1048576">200MB - 1GB</option>
                  <option value="1048577">1GB+</option>
                </select>
              </div>
            </div>
          </div>
        </div>
        <div class="cta">
          <button class="base__font btn dwnld" @click="filterTable()">
            Filter Table
          </button>
          <button class="base__font btn dwnld none">Clear Filter</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { clear } from "~/utils/svg.js";

const { filterCapsule, activeFilterCapsule } = defineProps({
  filterCapsule: {
    type: Array,
    required: true,
  },
  activeFilterCapsule: {
    type: Number,
    required: true,
  },
});

const selectedMessageRange = ref("");
const selectedMediaRange = ref("");

const emits = defineEmits(["filterCapsule", "handleCloseFilter"]);

function handleFilterCapsule(e) {
  emits("filterCapsule", e.index);
  getTimePeriod(e.data);
}

function close() {
  emits("handleCloseFilter");
}

const minDate = ref("");
const maxDate = ref("");

function getTimePeriod(period) {
  const today = new Date();
  switch (period) {
    case "Today":
      minDate.value = today.toISOString().split("T")[0];
      maxDate.value = today.toISOString().split("T")[0];
      break;
    case "Last 7 days":
      const lastWeek = new Date(
        today.getFullYear(),
        today.getMonth(),
        today.getDate() - 7
      );
      minDate.value = lastWeek.toISOString().split("T")[0];
      maxDate.value = today.toISOString().split("T")[0];
      break;
    case "This month":
      const firstDayOfMonth = new Date(today.getFullYear(), today.getMonth(), 1);
      minDate.value = firstDayOfMonth.toISOString().split("T")[0];
      maxDate.value = today.toISOString().split("T")[0];
      break;
    case "Last 3 months":
      const firstDayOfLast3Months = new Date(
        today.getFullYear(),
        today.getMonth() - 3,
        1
      );
      minDate.value = firstDayOfLast3Months.toISOString().split("T")[0];
      maxDate.value = today.toISOString().split("T")[0];
      break;
    default:
      console.error("Invalid time period");
      minDate.value = "";
      maxDate.value = "";
  }
}

function filterTable() {
  const option = {
    minDate,
    maxDate,
    selectedMessageRange,
    selectedMediaRange,
  };
  console.log(option);
}

onMounted(() => {
  handleFilterCapsule({ data: filterCapsule[0], index: 0 });
});
</script>

<style scoped>
.table_filter_wrap {
  background-color: #0714394d;
}

.table__filter {
  position: relative;
  width: 100%;
  height: 100vh;
  background: #fff;
  max-width: 588px;
  margin-left: auto;
  padding: 26px 40px;
}
.table__header {
  margin-bottom: 33px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.table__header h3 {
  font-size: 24px;
  line-height: 32px;
  font-weight: 400;
  color: #000000;
}

.table_filter_wrap {
  position: fixed;
  top: 0;
  right: 0;
  height: 100vh;
  width: 100vw;
  z-index: 9999;
}

.filter__wrap {
  margin-bottom: 35px;
  gap: 12px;
  align-items: center;
}

.filter__capsule {
  font-weight: 400;
  font-size: 14px;
  line-height: 16px;
  cursor: pointer;
  justify-content: center;
  align-items: center;
  padding: 10px 18px 10px 18px;
  border: 1px solid #eff1f6;
  border-radius: 100px;
}

.filter__capsule.active {
  background: #fff1e9;
  border-color: #f05a28;
  color: #f05a28;
}

.select__filter {
  margin-bottom: 26px;
  width: 100%;
  align-items: center;
  justify-content: space-between;
}

.filter__field {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 8px;
}

.filter__field label {
  font-weight: 500;
  font-size: 16px;
  line-height: 24px;
  color: #51545c;
}

.filter__field input.input {
  width: 100%;
  max-width: fit-content;
}

select {
  padding: 0;
}

.select.input {
  width: 100%;
  padding: 18px 20px;
}
.cta {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  margin-bottom: 20%;
}

.cta button {
  width: 100%;
  font-size: 16px;
  font-weight: 400;
}

.cta button.none {
  background: none;
  color: #8d9091;
}

.filter__wrap__group__cta {
  display: flex;
  flex-direction: column;
  height: 100%;
  justify-content: space-between;
}
</style>

<style>
.table__header .clear {
  background: #fcf5f5;
  justify-content: center;
  align-items: center;
  height: 26px;
  width: 26px;
  border-radius: 50px;
}
</style>
