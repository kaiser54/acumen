<template>
  <div style="max-width: 400px; width: 400px;">
    <canvas ref="acquisitionsChart"></canvas>
  </div>
</template>

<script setup>
import { ref, onMounted, defineProps } from "vue";
import Chart from "chart.js/auto";

const props = defineProps({
  data: {
    type: Array,
    required: true,
  },
  lineColor: {
    type: String,
    required: true,
  },
  fillColor: {
    type: String,
    required: true,
  },
});

const acquisitionsChart = ref(null);

onMounted(async () => {
  let data;
  if (props.data.length > 0 && props.data[0].day !== undefined) {
    // If the first element has a 'day' property, assume it's daily data
    data = props.data.map((item) => ({ label: item.day, value: item.count }));
  } else if (props.data.length > 0 && props.data[0].month !== undefined) {
    // If the first element has a 'month' property, assume it's monthly data
    data = props.data.map((item) => ({ label: item.month, value: item.count }));
  }

  if (!data) {
    console.error("Invalid data format provided.");
    return;
  }

  const ctx = acquisitionsChart.value.getContext("2d");

  new Chart(ctx, {
    type: "line",
    options: {
      animation: true,
      plugins: {
        legend: {
          display: false,
          labels: {
            font: {
              size: 12,
              weight: 400,
              family: "Hanken Grotesk",
            },
          },
        },
        tooltip: {
          enabled: true,
        },
      },
      scales: {
        y: {
          display: false, // Hide the y-axis label
        },
      },
    },
    data: {
      labels: data.map((row) => row.label),
      datasets: [
        {
          label: "Active Users",
          borderColor: props.lineColor,
          data: data.map((row) => row.value),
          backgroundColor: props.fillColor,
          fill: "start",
        },
      ],
    },
  });
});
</script>
