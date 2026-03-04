<template>
    <div class="note-card" :class="{ 'blocked': isBlocked }">

    <input
        v-model="note.title"
        type="text"
        class="title-input"
        placeholder="Card name"
        :disabled="isBlocked"
    >

    <div class="progress">
        
    </div>

    </div>
</template>