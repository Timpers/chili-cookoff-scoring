
<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import chilisData from './data/chilis.json'

interface Chili {
  id: number
  venue: string
  scores: {
    taste: number
    texture: number
    effort: number
  }
  notes: string
}

const chilis = ref<Chili[]>([])

const totalScore = (chili: Chili) => {
  return chili.scores.taste + chili.scores.texture + chili.scores.effort
}

const rankedChilis = computed(() => {
  return [...chilis.value].sort((a, b) => totalScore(b) - totalScore(a))
})

const winner = computed(() => {
  return rankedChilis.value[0]
})

const saveScores = () => {
  localStorage.setItem('chiliScores', JSON.stringify(chilis.value))
}

const loadScores = () => {
  const savedScores = localStorage.getItem('chiliScores')
  if (savedScores) {
    chilis.value = JSON.parse(savedScores)
  } else {
    chilis.value = chilisData
  }
}

onMounted(() => {
  loadScores()
})
</script>

<template>
  <div id="app">
    <header>
      <h1>Chili Cook-Off Scoring</h1>
    </header>
    <main>
      <div class="chili-list">
        <div v-for="chili in chilis" :key="chili.id" class="chili-item">
          <h2>{{ chili.venue }}</h2>
          <div class="scoring">
            <div class="score-item">
              <label>Taste: {{ chili.scores.taste }}</label>
              <input type="range" min="0" max="10" v-model.number="chili.scores.taste" @change="saveScores">
            </div>
            <div class="score-item">
              <label>Texture: {{ chili.scores.texture }}</label>
              <input type="range" min="0" max="10" v-model.number="chili.scores.texture" @change="saveScores">
            </div>
            <div class="score-item">
              <label>Effort: {{ chili.scores.effort }}</label>
              <input type="range" min="0" max="10" v-model.number="chili.scores.effort" @change="saveScores">
            </div>
            <div class="notes-item">
              <label>Notes:</label>
              <textarea v-model="chili.notes" @change="saveScores"></textarea>
            </div>
          </div>
          <div class="total-score">
            <strong>Total: {{ totalScore(chili) }}</strong>
          </div>
        </div>
      </div>
      <div class="results">
        <h2>Results</h2>
        <div v-if="winner && totalScore(winner) > 0">
          <h3>Winner: {{ winner.venue }}</h3>
          <p>Total Score: {{ totalScore(winner) }}</p>
        </div>
        <div v-else>
          <p>No scores yet. Start scoring to see the results.</p>
        </div>
        <ol>
          <li v-for="chili in rankedChilis" :key="chili.id">
            {{ chili.venue }}: {{ totalScore(chili) }}
          </li>
        </ol>
      </div>
    </main>
  </div>
</template>

<style>
body {
  background-color: #fdf5e6;
  color: #4a2c2a;
  font-family: sans-serif;
}

#app {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

header {
  text-align: center;
  margin-bottom: 20px;
}

h1 {
  font-family: 'Brush Script MT', cursive;
  font-size: 4em;
  color: #8b4513;
}

.chili-list {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 20px;
}

.chili-item {
  background-color: #fff;
  border: 2px solid #8b4513;
  border-radius: 10px;
  padding: 20px;
}

.chili-item h2 {
  margin-top: 0;
  color: #8b4513;
}

.scoring {
  margin-top: 10px;
}

.score-item, .notes-item {
  margin-bottom: 10px;
}

.score-item label, .notes-item label {
  display: block;
  margin-bottom: 5px;
}

input[type="range"] {
  width: 100%;
}

textarea {
  width: 100%;
  height: 80px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.total-score {
  margin-top: 10px;
  text-align: right;
  font-size: 1.2em;
}

.results {
  margin-top: 40px;
  background-color: #fff;
  border: 2px solid #8b4513;
  border-radius: 10px;
  padding: 20px;
}

.results h2 {
  margin-top: 0;
  color: #8b4513;
  text-align: center;
}

.results h3 {
  color: #8b4513;
}

ol {
  padding-left: 20px;
}
</style>
