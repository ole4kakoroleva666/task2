<template>
  <div class="app">
    <h1>Мои заметки</h1>
    <div class="columns">

      <div class="column">
        <h2>Column 1 (max 3 cards)</h2>
        <NoteCard 
                    v-for="note in notes" 
                    :key="note.id"
                    :note="note"
                    @update="saveNotes"
                />

      </div>

      <div class="column">
        <h2>Column 2 (max 5 cards)</h2>

      </div>

      <div class="column">
        <h2>Column 3(without restrictions)</h2>

      </div>

    </div>

    <button @click="addNewNote" class="add-card-btn">
            + Add new card
        </button>

  </div>
</template>

<script setup>
import { ref } from 'vue'
import NoteCard from './components/NoteCard.vue'

// Test data
const notes = ref([
    {
        id: 1,
        title: 'Shopping',
        items: [
            { text: 'Milk', completed: false },
            { text: 'Bread', completed: true },
            { text: 'Eggs', completed: false }
        ],
        completedAt: '2024-03-05'
    }
])

const addNewNote = () => {
    notes.value.push({
        id: Date.now(),
        title: 'New note',
        items: [
            { text: 'Item 1', completed: false },
            { text: 'Item 2', completed: false },
            { text: 'Item 3', completed: false }
        ],
        completedAt: null
    })
}

const saveNotes = () => {
    console.log('Saved!')
    // Here we'll save to localStorage later
}
</script>

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