<template>
  <div class="app">
    <h1>Мои заметки</h1>
    <div class="columns">

      <div class="column">
        <h2>Column 1 ({{ column1.length }}/ 3)</h2>
          <NoteCard 
            v-for="note in column1" 
            :key="note.id"
            :note="note"
            @update="updateNote"
          />

      </div>

      <div class="column">
        <h2>Column 2 ({{ column2.length }}/5)</h2>
          <NoteCard
            v-for="note in column2"
            :key="note.id"
            :note="note"
            @update="updateNote"
          />  
      </div>

      <div class="column">
        <h2>Column 3 ({{ column3.length }})</h2>
          <NoteCard 
            v-for="note in column3" 
            :key="note.id"
            :note="note"
            @update="updateNote"
          />
      </div>

    </div>

    <button v-if="column1.length < 3" @click="addNewNote" class="add-card-btn">
            + Add new card
    </button>
    <p v-else class="!!!"> Column 1 is full(max 3 cards)</p>

  </div>
</template>

<script setup>
import { ref } from 'vue'
import NoteCard from './components/NoteCard.vue'


const column1 = ref([
    {
        id: 1,
        title: 'Shopping',
        items: [
            { text: 'Milk', completed: false },
            { text: 'Bread', completed: true },
            { text: 'Eggs', completed: false }
        ],
        completedAt: null
    },

    {
        id: 2,
        title: 'Shopping',
        items: [
            { text: 'Milk', completed: false },
            { text: 'Bread', completed: true },
            { text: 'Eggs', completed: false }
        ],
        completedAt: null
    }
])

const column2 = ref([
 {
        id: 3,
        title: 'Shopping',
        items: [
            { text: 'Milk', completed: false },
            { text: 'Bread', completed: true },
            { text: 'Eggs', completed: false }
        ],
        completedAt: null
    },
])

const column3 = ref([])

const addNewNote = () => {
  if (column1.value.length < 3) {
    column1.value.push({
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
}

const updateNote = (updatedNote) => {
  console.log('Note updated:', updatedNote)
  moveNote(updatedNote)
}

const moveNote = (note) => {
  const total = note.items.length
  const done = note.items.filter(item => item.completed).length
  const percent = Math.round((done / total) * 100)

  let inCol1 = column1.value.findIndex(n => n.id === note.id)
  let inCol2 = column2.value.findIndex(n => n.id === note.id)

  if (inCol1 !== -1){
    if (percent === 100) {
      const [movedNote] =  column1.value.splice(inCol1,1)
      movedNote.completedAt = new Date().toLocaleString()
      column3.value.push(movedNote)
      console.log('Moved to column 3')
    }

    else if (percent > 50) {
      if (column2.value.length < 5){
      const [movedNote] = column1.value.splice(inCol1, 1)
      column2.value.push(movedNote)
      console.log('Moved to column 2')
    }else {
      console.log('Column 2 is full(max 5 cards)')
    }
  }
  }

    else if (inCol2 !== -1) {
      if (percent === 100) {
        const [movedNote] =  column2.value.splice(inCol2,1)
        movedNote.completedAt = new Date().toLocaleString()
        column3.value.push(movedNote)
        console.log('Moved to column 3')
      }
    }
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
}.add-card-btn {
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

.add-card-btn:hover {
  background-color: #84425d;
}

.warning {
  text-align: center;
  color: #dc3545;
  font-weight: bold;
}
</style>