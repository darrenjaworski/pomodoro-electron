<script setup lang="ts">
import PomodoroTimer from './components/PomodoroTimer.vue'
import SettingsScreen from './components/SettingsScreen.vue'
import { ref } from 'vue'

const showSettings = ref(false)
const workDuration = ref(25 * 60)
const breakDuration = ref(5 * 60)
const theme = ref('dark')

function updateWorkDuration(val: number) {
  workDuration.value = val
}
function updateBreakDuration(val: number) {
  breakDuration.value = val
}
function updateTheme(val: string) {
  theme.value = val
  document.documentElement.setAttribute('data-theme', val)
}

document.documentElement.setAttribute('data-theme', theme.value)
</script>

<template>
  <div>
    <button @click="showSettings = !showSettings" style="position:absolute;top:1rem;right:1rem;z-index:10;">{{
      showSettings ? 'Back' : 'Settings' }}</button>
    <SettingsScreen v-if="showSettings" :workDuration="workDuration" :breakDuration="breakDuration" :theme="theme"
      @update:workDuration="updateWorkDuration" @update:breakDuration="updateBreakDuration" @update:theme="updateTheme"
      @close="showSettings = false" />
    <PomodoroTimer v-else :workDuration="workDuration" :breakDuration="breakDuration" :theme="theme" />
  </div>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}

.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}

.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
