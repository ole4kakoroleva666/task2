<template>
  <div class="app">
    <h1>Мои заметки</h1>
    <div class="columns">

      <div class="column">
        <h2>Column 1 (max 3 cards)</h2>

      </div>

      <div class="column">
        <h2>Column 2 (max 5 cards)</h2>

      </div>

      <div class="column">
        <h2>Column 3(without restrictions)</h2>

      </div>

    </div>

    <button class="add-button"> Add card </button>
  </div>
</template>

<script setup></script>

<style scoped>
.app {
  padding: 20px;
  max-width: 1400px;
  margin: 0 auto;
}

h1 {
  margin-bottom: 30px;
  color: #6f2a46;
  text-align: center;
}

.columns {
  display: flex;
  gap: 20px;
  margin-bottom: 30px;
}

.column {
  flex: 1;
  background: #f8f9fa;
  border-radius: 8px;
  padding: 16px;
  min-height: 500px;
}

.column h2 {
  font-size: 18px;
  margin-bottom: 16px;
  color: #6f2a46;
  padding-bottom: 8px;
  border-bottom: 2px solid #84425d;
}

.add-button {
  display: block;
  margin: 0 auto;
  padding: 12px 24px;
  background-color: #6f2a46;
  color: white;
  border: none;
  border-radius: 6px;
  font-size: 16px;
  cursor: pointer;
}

.add-button:hover {
  background-color: #84425d;
}
</style>