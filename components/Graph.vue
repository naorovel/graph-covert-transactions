<template>
    <div>
      <h2>Transaction Graph</h2>
      <canvas id="graphCanvas"></canvas>
    </div>
  </template>
  
  <script>
  import { onMounted, watch } from "vue";
  
  export default {
    props: {
      transactions: Array, // Receive transactions as a prop
    },
    setup(props) {
      let ORB = null; // Will hold ORB.js after it's dynamically imported
  
      // Load ORB.js only on the client side
      onMounted(async () => {
        if (process.client) {
          ORB = await import("orb.js");
          drawGraph(props.transactions);
        }
      });
  
      // Watch for changes in transactions and redraw
      watch(() => props.transactions, (newTransactions) => {
        if (ORB) drawGraph(newTransactions);
      });
  
      const drawGraph = (transactions) => {
        if (!transactions.length) return;
  
        const nodes = new Map();
        const edges = [];
  
        transactions.forEach((tx) => {
          if (!nodes.has(tx.from)) nodes.set(tx.from, { id: tx.from });
          if (!nodes.has(tx.to)) nodes.set(tx.to, { id: tx.to });
  
          edges.push({ source: tx.from, target: tx.to, weight: tx.value });
        });
  
        // Ensure ORB.js is loaded
        if (!ORB) return;
  
        const graph = new ORB.Graph([...nodes.values()], edges, {
          canvas: document.getElementById("graphCanvas"),
          directed: true, // Ethereum transactions have a direction
          force: 0.1, // Adjust physics settings
        });
  
        graph.render();
      };
    },
  };
  </script>
  
  <style>
  #graphCanvas {
    width: 100%;
    height: 500px;
    border: 1px solid black;
  }
  </style>
  