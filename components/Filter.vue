<template>
  <div class="table_filter_wrap">
    <div class="table__filter">
      <div class="table__header">
        <h3>Filter Table</h3>
        <span v-html="clear" class="clear flex" @click="close()"></span>
      </div>
      <div class="filter__wrap flex">
        <div
          class="filter__capsule flex"
          :class="{ active: index == activeFilterCapsule }"
          v-for="(data, index) in filterCapsule"
          :key="data"
          @click="handleFilterCapsule(index)"
        >
          {{ data }}
        </div>
      </div>
      <div class="select__filter flex">
        <div class="filter__field">
          <label for="">Date From</label>
          <input class="input" type="date" name="" id="" />
        </div>
        <div class="filter__field">
          <label for="">Date To</label>
          <input class="input" type="date" name="" id="" />
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
            <select name="" id="">
              <option value="" disabled selected>Please select</option>
              <option value="">0 - 50 MB</option>
              <option value="">50 MB - 200MB</option>
              <option value="">200MB - 1GB</option>
              <option value="">1GB+</option>
            </select>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { clear } from "~/utils/svg.js";

const {} = defineProps({
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

const emits = defineEmits(["filterCapsule", "handleCloseFilter"]);

function handleFilterCapsule(index) {
  emits("filterCapsule", index);
}

function close() {
  emits("handleCloseFilter");
}
</script>

<style scoped>
.table_filter_wrap {
  background-color: #0714394d;
}

.table__filter {
  width: 100%;
  height: 100vh;
  background: #fff;
  max-width: 588px;
  margin-left: auto;
  padding: 26px 40px;
}
.table__header {
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
