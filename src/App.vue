<script setup lang="ts">
import { computed, ref } from "vue";
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
} from "chart.js";
import { Bar } from "vue-chartjs";

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
);

enum Unit {
  KB = 1024,
  MB = 1024 ** 2,
  GB = 1024 ** 3,
  TB = 1024 ** 4,
}

const value = ref<number>(0);
const fromUnit = ref<Unit>(Unit.KB);
const toUnit = ref<Unit>(Unit.MB);
const convertedValue = ref<number>(0);
const numbers = ref<number[]>([0]);

const unitOptions = [
  { name: "KB", value: Unit.KB },
  { name: "MB", value: Unit.MB },
  { name: "GB", value: Unit.GB },
  { name: "TB", value: Unit.TB },
];

const handleConvert = () => {
  if (!value.value || !fromUnit.value || !toUnit.value) return;
  convertedValue.value = convertUnit(value.value, fromUnit.value, toUnit.value);
};

const convertUnit = (value: number, from: Unit, to: Unit) => {
  return (value * from) / to;
};

const getUnitName = (unit: Unit): string => {
  const unitObj = unitOptions.find((option) => option.value === unit);
  return unitObj ? unitObj.name : "Unknow";
};

const addNumber = () => {
  numbers.value.push(0);
};

const removeNumber = (index: number) => {
  numbers.value.splice(index, 1);
};

const stats = computed(() => {
  if (numbers.value.length === 0) {
    return null;
  }

  const moyenne =
    numbers.value.reduce((sum, val) => sum + val, 0) / numbers.value.length;

  const variance =
    numbers.value.reduce((sum, val) => sum + (val - moyenne) ** 2, 0) /
    numbers.value.length;

  const ecartType = Math.sqrt(variance);

  const total = numbers.value.reduce((sum, val) => sum + val, 0);

  return { moyenne, variance, ecartType, total };
});

const chartData = computed(() => {
  if (!stats.value) return null;

  return {
    labels: ["Moyenne", "Variance", "Écart-type", "Total"],
    datasets: [
      {
        label: "Valeurs des statistiques",
        data: [
          stats.value.moyenne,
          stats.value.variance,
          stats.value.ecartType,
          stats.value.total,
        ],
        backgroundColor: ["#FF6384", "#36A2EB", "#FFCE56", "#4BC0C0"],
      },
    ],
  };
});

const chartOptions = {
  responsive: true,
};
</script>

<template>
  <div class="container">
    <h1>Convertisseur d'unités</h1>
    <div>
      <label for="value">Valeur : </label>
      <input
        id="value"
        v-model="value"
        type="number"
        placeholder="Entrer une valeur"
        class="input"
      />
    </div>

    <div>
      <label for="from">De : </label>
      <select id="from" v-model="fromUnit" class="input">
        <option
          v-for="unit in unitOptions"
          :key="unit.name"
          :value="unit.value"
        >
          {{ unit.name }}
        </option>
      </select>
    </div>

    <div>
      <label for="to">À : </label>
      <select id="to" v-model="toUnit" class="input">
        <option
          v-for="unit in unitOptions"
          :key="unit.name"
          :value="unit.value"
        >
          {{ unit.name }}
        </option>
      </select>
    </div>

    <button @click="handleConvert" class="button">Convertir</button>

    <p v-if="convertedValue">
      Résultat : {{ convertedValue }} {{ getUnitName(toUnit) }}
    </p>

    <hr />
    <h1>Calculateur de statistiques</h1>
    <div v-for="(_num, index) in numbers" :key="index">
      <input
        v-model="numbers[index]"
        type="number"
        placeholder="Entrez un nombre"
      />
      <button @click="removeNumber(index)" class="remove-button">X</button>
    </div>
    <button @click="addNumber" class="button">Ajouter un nombre</button>
    <table v-if="stats" class="stats-table">
      <thead>
        <tr>
          <th>Statistique</th>
          <th>Valeur</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>Moyenne</td>
          <td>{{ stats.moyenne }}</td>
        </tr>
        <tr>
          <td>Variance</td>
          <td>{{ stats.variance }}</td>
        </tr>
        <tr>
          <td>Écart-type</td>
          <td>{{ stats.ecartType }}</td>
        </tr>
        <tr>
          <td>Total</td>
          <td>{{ stats.total }}</td>
        </tr>
      </tbody>
    </table>

    <div v-if="chartData">
      <h2>Visualisation</h2>
      <Bar :data="chartData" :options="chartOptions" />
    </div>
  </div>
</template>

<style scoped></style>
