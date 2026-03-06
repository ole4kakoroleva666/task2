<template>
  <div class="app">
    <h1>My notes</h1>

      <button v-if="column1.length < 3 && !hasActivePriority" @click="addNewNote(false)" class="add-card-btn">
            + Add new card
      </button>

      <button v-if="column1.length < 3 && !hasActivePriority" @click="addNewNote(true)" class="add-card-btn priority-btn">
       + Add priority card
      </button>

    <div v-if="hasActivePriority" class="global-block">
      PRIORITY TASK ACTIVE
      <p>The whole app is blocked until you complete the priority task</p>
    </div>  

    <p v-else class="!!!"> Column 1 is full(max 3 cards)</p>

    <div class="columns">

      <div class="column" :class="{'column-blocked': isColumn1Blocked}">
        <div class="column-header">
        <h2>Column 1 ({{ column1.length }}/ 3)</h2>
        <div v-if="isColumn1Blocked" class="blocked-overlay">
          <p>1 column filled. Free up space in 2 columns</p>
        </div>
      </div>
          <NoteCard 
            v-for="note in column1" 
            :key="note.id"
            :note="note"
            :isBlocked="isColumn1Blocked || (hasActivePriority && !note.priority)"
            :globalBlocked="hasActivePriority"
            @update="updateNote"
          />

      </div>

      <div class="column">
        <h2>Column 2 ({{ column2.length }}/5)</h2>
          <NoteCard
            v-for="note in column2"
            :key="note.id"
            :note="note"
            :isBlocked="hasActivePriority" 
            :globalBlocked="hasActivePriority && !note.priority" 
            @update="updateNote"
          />  
      </div>

      <div class="column">
        <h2>Column 3 ({{ column3.length }})</h2>
          <NoteCard 
            v-for="note in column3" 
            :key="note.id"
            :note="note"
            :isBlocked="false" 
            :globalBlocked="hasActivePriority"   
            @update="updateNote"
          />
      </div>

    </div>

  </div>
</template>

<script setup>
import { ref } from 'vue'
import NoteCard from './components/NoteCard.vue'


const column1 = ref([
    {
        id: 1,
        title: 'Shopping',
        priority: false,
        items: [
            { text: 'Item 1', completed: false },
            { text: 'Item 2', completed: true },
            { text: 'Item 3', completed: false }
        ],
        completedAt: null
    },

    {
        id: 2,
        title: 'Shopping',
        priority: false,
        items: [
            { text: 'Item 1', completed: false },
            { text: 'Item 2', completed: true },
            { text: 'Item 3', completed: false }
        ],
        completedAt: null
    }
])

const column2 = ref([
 {
        id: 3,
        title: 'Shopping',
        priority: false,
        items: [
            { text: 'Item 1', completed: false },
            { text: 'Item 2', completed: true },
            { text: 'Item 3', completed: false }
        ],
        completedAt: null
    },
])

const column3 = ref([])
const isColumn1Blocked = ref(false)
const hasActivePriority = ref(false)

const addNewNote = (makePriority = false) => {
  if (column1.value.length < 3) {
    
    if (makePriority && hasActivePriority.value) {
      alert('Cannot create another priority card. Complete the current one first.')
      return
    }

     if (hasActivePriority.value) {
    alert('Cannot create new cards while a priority task is active!')
    return
  }
    
    column1.value.push({
      id: Date.now(),
      title: 'New note',
      priority: makePriority,
      items: [
        { text: 'Item 1', completed: false },
        { text: 'Item 2', completed: false },
        { text: 'Item 3', completed: false }
      ],
      completedAt: null
    })
    
    saveToStorage()
    
    if (makePriority) {
      console.log('Priority card created')
    }
  }  
}
const updateNote = (updatedNote) => {
  console.log('Note updated:', updatedNote)
  moveNote(updatedNote)
  checkBlocking()
  checkPriority()
  saveToStorage() 
}

const moveNote = (note) => {
  const total = note.items.length
  const done = note.items.filter(item => item.completed).length
  const percent = Math.round((done / total) * 100)

  let inCol1 = column1.value.findIndex(n => n.id === note.id)
  let inCol2 = column2.value.findIndex(n => n.id === note.id)
  let inCol3 = column3.value.findIndex(n => n.id === note.id)

  if (inCol1 !== -1) {
    if (percent === 100) {
      const [movedNote] = column1.value.splice(inCol1, 1)
      movedNote.completedAt = new Date().toLocaleString()
      column3.value.push(movedNote)
      console.log('Moved to column 3')
    } else if (percent > 50) {
      if (column2.value.length < 5) {
        const [movedNote] = column1.value.splice(inCol1, 1)
        column2.value.push(movedNote)
        console.log('Moved to column 2')
      } else {
        console.log('Column 2 is full (max 5 cards)')
      }
    }
  } 
  else if (inCol2 !== -1) {
    if (percent === 100) {
      const [movedNote] = column2.value.splice(inCol2, 1)
      movedNote.completedAt = new Date().toLocaleString()
      column3.value.push(movedNote)
      console.log('Moved to column 3')
    } else if (percent <= 50) {
      if (column1.value.length < 3) {
        const [movedNote] = column2.value.splice(inCol2, 1)
        movedNote.completedAt = null
        column1.value.push(movedNote)
        console.log('Moved back to column 1')
      } else {
        console.log('Column 1 is full! Cannot move back')
      }
    }
  }
  else if (inCol3 !== -1) {
    if (percent < 100) {
      
      
      note.completedAt = null
      
      if (percent > 50) {
        if (column2.value.length < 5) {
          const [movedNote] = column3.value.splice(inCol3, 1)
          column2.value.push(movedNote)
          console.log('Moved from column 3 to column 2')
        } else {
          console.log('Column 2 is full! Cannot move')
        }
      } else {
        if (column1.value.length < 3) {
          const [movedNote] = column3.value.splice(inCol3, 1)
          column1.value.push(movedNote)
          console.log('Moved from column 3 to column 1')
        } else {
          console.log('Column 1 is full! Cannot move')
        }
      }
    }
  }
}
const checkBlocking = () => {
  const isColumn2Full = column2.value.length >= 5
  let hasOver50InColumn1 = false
  
  for (let note of column1.value) {
    const total = note.items.length
    const done = note.items.filter(item => item.completed).length
    const percent = Math.round((done / total) * 100)
    if (percent > 50 && percent < 100) {
      hasOver50InColumn1 = true
      break
    }
  }
  
  if (isColumn2Full && hasOver50InColumn1) {
    isColumn1Blocked.value = true
    console.log('Column 1 locked')
  } else {
    if (isColumn1Blocked.value === true) {
      isColumn1Blocked.value = false
      console.log('Column 1 unlocked')
    }
  }
}

const checkPriority = () => {
  const activePriority = [...column1.value, ...column2.value].find(note => 
    note.priority === true
  )
  hasActivePriority.value = !!activePriority
  
  if (hasActivePriority.value) {
    console.log('Active priority card exists')
  }
}
const saveToStorage = () => {
  const data = {
    column1: column1.value,
    column2: column2.value,
    column3: column3.value
  }
  localStorage.setItem('notesApp', JSON.stringify(data))
  console.log('data is saved')
}

const loadFromStorage = () => {
  const savedData = localStorage.getItem('notesApp')
  if (savedData) {
    try {
      const data = JSON.parse(savedData)
      column1.value = data.column1 || []
      column2.value = data.column2 || []
      column3.value = data.column3 || []
      console.log('Data loaded from storage')
    } catch (e) {
      console.error('Error loading data:', e)
    }
  } 
}

loadFromStorage()
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


.column-blocked {
  background-color: #ffe9e9;
  border: 2px solid;
  opacity: 0.8;
}

.blocked-overlay {
  background-color: #dc3545;
  color: white;
  padding: 10px;
  margin: 10px 0;
  border-radius: 4px;
  text-align: center;
  font-weight: bold;
}

</style>