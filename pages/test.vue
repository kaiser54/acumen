<template>
  <table class="table-sortable">
    <thead>
      <tr>
        <th
          @click="sortTable(0)"
          :class="{
            'th-sort-asc': sortedColumnIndex === 0 && ascending,
            'th-sort-desc': sortedColumnIndex === 0 && !ascending,
          }"
        >
          Rank
        </th>
        <th
          @click="sortTable(1)"
          :class="{
            'th-sort-asc': sortedColumnIndex === 1 && ascending,
            'th-sort-desc': sortedColumnIndex === 1 && !ascending,
          }"
        >
          Name
        </th>
        <th
          @click="sortTable(2)"
          :class="{
            'th-sort-asc': sortedColumnIndex === 2 && ascending,
            'th-sort-desc': sortedColumnIndex === 2 && !ascending,
          }"
        >
          Age
        </th>
        <th
          @click="sortTable(3)"
          :class="{
            'th-sort-asc': sortedColumnIndex === 3 && ascending,
            'th-sort-desc': sortedColumnIndex === 3 && !ascending,
          }"
        >
          Occupation
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(row, index) in sortedRows" :key="index">
        <td v-for="(cell, cellIndex) in row" :key="cellIndex">{{ cell }}</td>
      </tr>
    </tbody>
  </table>
</template>

<script setup>
import { ref, onMounted } from "vue";

const tableData = ref([
  { rank: 1, name: "Dom", age: 35, occupation: "Web Developer" },
  { rank: 2, name: "Rebecca", age: 29, occupation: "Teacher" },
  { rank: 3, name: "John", age: 30, occupation: "Civil Engineer" },
  { rank: 4, name: "Andre", age: 20, occupation: "Dentist" },
]);

const sortedColumnIndex = ref(0);
const ascending = ref(true);
const sortedRows = ref([]);

const sortTable = (column) => {
  ascending.value = sortedColumnIndex.value === column ? !ascending.value : true;
  sortedColumnIndex.value = column;
  sortedRows.value = sortRows(column);
};

const sortRows = (column) => {
  const dirModifier = ascending.value ? 1 : -1;
  return tableData.value.sort((a, b) => {
    const aColText = a[Object.keys(a)[column]].toString().trim();
    const bColText = b[Object.keys(b)[column]].toString().trim();
    return aColText.localeCompare(bColText) * dirModifier;
  });
};

onMounted(() => {
  sortTable(0); // Default sorting by the first column
});
</script>

<style>
.table-sortable th {
  cursor: pointer;
}

.table-sortable .th-sort-asc::after {
  content: "\25b4";
}

.table-sortable .th-sort-desc::after {
  content: "\25be";
}

.table-sortable .th-sort-asc::after,
.table-sortable .th-sort-desc::after {
  margin-left: 5px;
}

.table-sortable .th-sort-asc,
.table-sortable .th-sort-desc {
  background: rgba(0, 0, 0, 0.1);
}
</style>
