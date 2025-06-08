<template>
    <div class="pomodoro-app" :class="{ 'final-minute': isFinalMinute }">
        <div class="timer-display">{{ minutes }}:{{ seconds < 10 ? '0' + seconds : seconds }}</div>
                <div class="controls">
                    <button @click="toggleTimer">{{ isRunning ? 'Pause' : 'Start' }}</button>
                    <button @click="resetTimer">Reset</button>
                    <button @click="switchSession">Switch to {{ isWorkSession ? 'Break' : 'Work' }}</button>
                </div>
                <div class="session-label">{{ sessionLabel }}</div>
        </div>
</template>

<script setup lang="ts">
import { ref, computed, watch, onUnmounted, watchEffect } from 'vue'

const props = defineProps<{ workDuration: number, breakDuration: number, theme: string }>()

const isRunning = ref(false)
const isWorkSession = ref(true)
const timeLeft = ref(props.workDuration)
let timer: number | null = null
const alarmAudio = new Audio('/alarm.mp3')

const minutes = computed(() => Math.floor(timeLeft.value / 60))
const seconds = computed(() => timeLeft.value % 60)
const sessionLabel = computed(() => isWorkSession.value ? 'Work Session' : 'Break')
const isFinalMinute = computed(() => isWorkSession.value && timeLeft.value <= 60)

function toggleTimer() {
    isRunning.value = !isRunning.value
    if (isRunning.value) {
        timer = setInterval(() => {
            if (timeLeft.value > 0) {
                timeLeft.value--
            } else {
                alarmAudio.currentTime = 0
                alarmAudio.play()
                isWorkSession.value = !isWorkSession.value
                timeLeft.value = isWorkSession.value ? props.workDuration : props.breakDuration
            }
        }, 1000)
    } else {
        if (timer) clearInterval(timer)
    }
}

function resetTimer() {
    if (timer) clearInterval(timer)
    isRunning.value = false
    timeLeft.value = isWorkSession.value ? props.workDuration : props.breakDuration
}

function switchSession() {
    isWorkSession.value = !isWorkSession.value
    timeLeft.value = isWorkSession.value ? props.workDuration : props.breakDuration
    isRunning.value = false
    if (timer) clearInterval(timer)
}

watch(() => props.workDuration, (val) => {
    if (isWorkSession.value) timeLeft.value = val
})
watch(() => props.breakDuration, (val) => {
    if (!isWorkSession.value) timeLeft.value = val
})

watch(isRunning, (running) => {
    if (!running && timer) clearInterval(timer)
})

watchEffect(() => {
    if (isFinalMinute.value) {
        document.documentElement.classList.add('final-minute')
        document.body.classList.add('final-minute')
    } else {
        document.documentElement.classList.remove('final-minute')
        document.body.classList.remove('final-minute')
    }
})

onUnmounted(() => {
    if (timer) clearInterval(timer)
})
</script>

<style scoped>
.pomodoro-app {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    min-height: 60vh;
    font-family: 'Inter', sans-serif;
}

.timer-display {
    font-size: 4rem;
    font-weight: bold;
    margin-bottom: 1rem;
    color: #333;
}

.controls button {
    margin: 0 0.5rem;
    padding: 0.5rem 1.5rem;
    font-size: 1.2rem;
    border: none;
    border-radius: 6px;
    background: #e0e0e0;
    color: #333;
    cursor: pointer;
    transition: background 0.2s;
}

.controls button:hover {
    background: #bdbdbd;
}

.session-label {
    margin-top: 1.5rem;
    font-size: 1.1rem;
    color: #888;
}

.final-minute .timer-display {
    color: red;
}

.pomodoro-app.final-minute {
    background: #e3f2fd !important;
    /* light blue */
    transition: background 0.5s;
}

:root.final-minute {
    background: #e3f2fd !important;
    transition: background 0.5s;
}
</style>
