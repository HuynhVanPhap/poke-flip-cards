<script setup>
import { RouterLink, RouterView } from 'vue-router'
import { reactive, ref } from 'vue'
import MainScreen from './components/MainScreen.vue'
import InteractScreen from './components/InteractScreen.vue'
import ResultScreen from './components/ResultScreen.vue'
import CopyRightScreen from './components/CopyRightScreen.vue'

import { shuffled } from '../src/utils/arrays.js'
import CopyRightScreenVue from './components/CopyRightScreen.vue'

const statusMatch = ref('default');
const timer = ref(0);
const settings = reactive({
  totalOfBlocks: 0,
  cardsContext: [],
  startedAt: null,
});

function handleBeforeStart(config) {
  console.log("Running handle before start, ", config);

  settings.totalOfBlocks = config.totalOfBlocks;

  const firstCards = Array.from(
      { length: settings.totalOfBlocks / 2 },
      (_, i) => i + 1
    );
  const secondCards = [...firstCards];
  const cards = [...firstCards, ...secondCards];

  settings.cardsContext = shuffled(cards);
  settings.startedAt = new Date().getTime();

  statusMatch.value = 'match';
}

function onGetResult() {
  timer.value = new Date().getTime() - settings.startedAt;

  statusMatch.value = 'result';
}

function handleStartAgain() {
  statusMatch.value = 'default';
}
</script>

<template>
  <main-screen v-if="statusMatch === 'default'" @onStart="handleBeforeStart($event)" />
  <interact-screen v-if="statusMatch === 'match'" :cardsContext="settings.cardsContext" @onFinish="onGetResult" />
  <result-screen v-if="statusMatch === 'result'" :timer="timer" @onStartAgain="handleStartAgain" />
  <p class="copyright">
    <copy-right-screen />
  </p>
</template>

<style scoped>
.copyright {
  position: fixed;
  left: 50%;
  transform: translateX(-50%);
  bottom: 1.5rem;
  color: var(--light);
  z-index: 3;
  font-size: 1.5rem;
}

.copyright a {
  color: #f4dc26;
}
</style>
