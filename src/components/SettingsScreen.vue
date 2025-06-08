<template>
    <div class="settings">
        <h2>Settings</h2>
        <div class="setting-group">
            <label>Work Duration (minutes):</label>
            <input type="number" v-model.number="work" min="1" max="120" />
        </div>
        <div class="setting-group">
            <label>Break Duration (minutes):</label>
            <input type="number" v-model.number="breakTime" min="1" max="60" />
        </div>
        <div class="setting-group">
            <label>Theme:</label>
            <select v-model="theme">
                <option value="light">Light</option>
                <option value="dark">Dark</option>
            </select>
        </div>
        <button @click="saveSettings">Save</button>
    </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
const props = defineProps<{
    workDuration: number,
    breakDuration: number,
    theme: string
}>()
const emit = defineEmits(['update:workDuration', 'update:breakDuration', 'update:theme', 'close'])

const work = ref(props.workDuration / 60)
const breakTime = ref(props.breakDuration / 60)
const theme = ref(props.theme)

function saveSettings() {
    emit('update:workDuration', work.value * 60)
    emit('update:breakDuration', breakTime.value * 60)
    emit('update:theme', theme.value)
    emit('close')
}
</script>

<style scoped>
.settings {
    max-width: 350px;
    margin: 2rem auto;
    padding: 2rem;
    border-radius: 10px;
    background: var(--settings-bg, #f5f5f5);
    color: var(--settings-color, #222);
    box-shadow: 0 2px 12px #0001;
}

.setting-group {
    margin-bottom: 1.5rem;
    display: flex;
    flex-direction: column;
}

.setting-group label {
    margin-bottom: 0.5rem;
}

input[type="number"],
select {
    padding: 0.5rem;
    border-radius: 5px;
    border: 1px solid #ccc;
    font-size: 1rem;
}

button {
    padding: 0.7rem 2rem;
    border: none;
    border-radius: 6px;
    background: #333;
    color: #fff;
    font-size: 1.1rem;
    cursor: pointer;
    transition: background 0.2s;
}

button:hover {
    background: #555;
}
</style>
