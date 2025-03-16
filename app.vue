<template>
  <div>
    <h1>Ethereum Transactions</h1>
    
    <button @click="loadCSV">Load Transactions</button>
    
    <table v-if="transactions.length">
      <thead>
        <tr>
          <th>Hash</th>
          <th>From</th>
          <th>To</th>
          <th>Value (ETH)</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="tx in transactions" :key="tx.hash">
          <td>{{ tx.hash }}</td>
          <td>{{ tx.from }}</td>
          <td>{{ tx.to }}</td>
          <td>{{ tx.value }}</td>
        </tr>
      </tbody>
    </table>

    
  </div>
</template>

<script>
import Papa from "papaparse";
import Graph from "@/components/Graph.vue"; // Import Graph component

export default {
  components: { Graph },
  data() {
    return {
      transactions: [],
    };
  },
  methods: {
    async loadCSV() {
      const response = await fetch("/Dataset.csv");
      const csvText = await response.text();
      Papa.parse(csvText, {
        header: true, // Treat first row as headers
        dynamicTyping: true, // Convert numbers automatically
        complete: (result) => {
          this.transactions = result.data;
        },
      });
    },
  },
};
</script>

<style>
table {
  width: 100%;
  border-collapse: collapse;
}
th, td {
  border: 1px solid #ddd;
  padding: 8px;
}
</style>
