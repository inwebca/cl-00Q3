<template>
  <div class="container">
    <h1>Calculateur de Statistiques</h1>

    <!-- Champs dynamiques -->
    <div v-for="(num, index) in numbers" :key="index" class="input-group">
      <input
          v-model.number="numbers[index]"
          type="number"
          placeholder="Entrez un nombre"
          class="input"
      />
      <button @click="removeNumber(index)" class="remove-button">X</button>
    </div>
    <button @click="addNumber" class="button">Ajouter un nombre</button>

    <!-- Bouton pour calculer -->
    <button @click="calculateStatistics" class="button">Calculer</button>

    <!-- Tableau des statistiques -->
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
        <td>{{ stats.mean }}</td>
      </tr>
      <tr>
        <td>Variance</td>
        <td>{{ stats.variance }}</td>
      </tr>
      <tr>
        <td>Écart-type</td>
        <td>{{ stats.stdDev }}</td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";

// Données réactives
const numbers = ref<number[]>([0]); // Liste initiale avec un champ
const stats = ref<{ mean: number; variance: number; stdDev: number } | null>(null);

// Ajouter un champ
const addNumber = () => {
  numbers.value.push(0);
};

// Supprimer un champ
const removeNumber = (index: number) => {
  numbers.value.splice(index, 1);
};

// Calculer les statistiques
const calculateStatistics = () => {
  if (numbers.value.length === 0) {
    stats.value = null;
    return;
  }

  const mean = numbers.value.reduce((sum, val) => sum + val, 0) / numbers.value.length;
  const variance =
      numbers.value.reduce((sum, val) => sum + (val - mean) ** 2, 0) / numbers.value.length;
  const stdDev = Math.sqrt(variance);

  stats.value = { mean, variance, stdDev };
};
</script>

