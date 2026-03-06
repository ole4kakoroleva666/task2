<template>
    <div class="card">

    <input
        v-model="note.title"
        type="text"
        placeholder="Card name"
        class="title-input"
        :disabled="isBlocked || globalBlocked"
        @input="emit('update', note)"
    >

    <div class="progress-box">
        Progress: {{ doneCount }} / {{ totalCount }} ({{ percent }}%)
    </div>

    <div v-if="note.priority" class="priority-badge">PRIORITY</div>

    <div v-for="(item, index) in note.items" :key="index" class="item-row">

        <input
            type="checkbox"
            v-model="item.completed"
            :disabled="isBlocked || globalBlocked"
            @change="emit('update', note)"
        >

        <input
            v-model="item.text"
            placeholder="Item text"
            class="item-input"
            :disabled="isBlocked || globalBlocked"
            @input="emit('update', note)"
        >
    </div>

        <button
            v-if="note.items.length < 5" @click="addItem"
                    class="add-btn" :disabled="isBlocked || globalBlocked" > + Add item
        </button>
    <div v-if="note.completedAt" class="date">
        Done: {{ note.completedAt }}
    </div>
</div>
</template>

    
<script setup>
import { computed } from 'vue'

const props = defineProps({
    note: Object,
    isBlocked: Boolean,
    globalBlocked: Boolean
})

const togglePriority = () => {
  if (!props.isBlocked) {
    props.note.priority = !props.note.priority
    emit('update', props.note)
  }
}

const emit = defineEmits(['update'])

const totalCount = computed(() => props.note.items.length)

const doneCount = computed(() => {
    return props.note.items.filter(item => item.completed).length
})

const percent = computed(() => {
    if (totalCount.value === 0) return 0
    return Math.round((doneCount.value / totalCount.value) * 100)
})

const addItem = () => {
    if (props.note.items.length < 5) {
        props.note.items.push({
            text: '',
            completed: false
        })
        emit('update', props.note)
    }
}
</script>

<style scoped>
.card {
    background: white;
    border: 1px solid #b9b0ad;
    border-radius: 8px;
    padding: 15px;
    margin-bottom: 15px;
}

.title-input {
    width: 100%;
    padding: 8px;
    margin-bottom: 10px;
    border: 1px solid #b9b0ad;
    border-radius: 4px;
    font-size: 16px;
}

.title-input:disabled,
.item-input:disabled,
.add-btn:disabled {
    background-color: #f0f0f0;
    opacity: 0.6;
    cursor: not-allowed;
}

input[type="checkbox"]:disabled {
    cursor: not-allowed;
    opacity: 0.6;
}


.progress-box {
    background: #855669;
    padding: 8px;
    margin: 10px 0;
    border-radius: 4px;
    text-align: center;
    color: #1c1c1b;
}

.item-row {
    display: flex;
    gap: 10px;
    margin-bottom: 8px;
    align-items: center;
}

.item-input {
    flex: 1;
    padding: 6px;
    border: 1px solid #b9b0ad;
    border-radius: 4px;
}

.add-btn {
    width: 100%;
    padding: 8px;
    margin-top: 10px;
    background: #855669;
    border: 1px;
    border-radius: 4px;
    cursor: pointer;
}

.add-btn:hover {
    background: #60414e;
}

.add-btn:disabled:hover {
    background: #855669;
}

.date {
    margin-top: 10px;
    padding-top: 10px;
    border-top: 1px solid #1c1c1b;
    font-size: 12px;
    color: #1c1c1b;
    text-align: right;
}

.priority-badge {
    color: #baba54;
}

.priority-task {
  display: flex;
  align-items: center;
  gap: 5px;
  margin-bottom: 10px;
  cursor: pointer;
  user-select: none;
}

.priority-icon {
  font-size: 24px;
  transition: transform 0.2s;
  color: #fef600;
}

.priority-icon:hover {
  transform: scale(1.2);
}

.priority-active {
  color: gold;
  text-shadow: 0 0 5px orange;
}

.priority-text {
  font-size: 14px;
  color: #666;
}
</style>
