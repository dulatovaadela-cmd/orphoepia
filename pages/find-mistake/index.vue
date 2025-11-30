<template>
  <div class="min-h-screen relative bg-gradient-to-br from-yellow-100 via-pink-50 to-purple-100 py-8 flex items-center justify-center">

    <!-- 🔷 ОКНО НУМЕРАЦИИ -->
    <div
      class="absolute top-4 right-4 grid grid-cols-5 gap-2 p-3 bg-white rounded-xl shadow-lg border border-yellow-200 w-72"
    >
      <div
        v-for="(t, index) in tasks"
        :key="index"
        @click="jumpTo(index)"
        class="w-10 h-10 flex items-center justify-center rounded-lg cursor-pointer border font-semibold text-base transition"
        :class="[
          currentIdx === index
            ? 'bg-blue-200 border-blue-500 text-blue-800'
            : answered[index]
              ? 'bg-green-200 border-green-500 text-green-800'
              : 'bg-white border-gray-300 text-gray-700 hover:bg-gray-100'
        ]"
      >
        {{ index + 1 }}
      </div>
    </div>

    <!-- 🟣 ОСНОВНОЙ БЛОК -->
    <div class="w-full max-w-2xl bg-white rounded-2xl shadow-2xl p-8 border-t-4 border-yellow-400">
      <h2 class="text-3xl font-bold text-center mb-2 text-purple-700">✍️ Найди ошибку в ударении</h2>
      <p class="text-center text-gray-500 mb-6">В каждом ряду одно слово с неправильным ударением. Введите его правильно.</p>

      <div v-if="!finished">

        <div class="mb-4 text-lg font-semibold text-center text-yellow-700">
          Вопрос {{ currentIdx + 1 }} из {{ tasks.length }}
        </div>

        <div class="text-center text-xl font-medium text-purple-800 mb-6">
          {{ currentTask.row }}
        </div>

        <input
          v-model="userAnswer"
          :disabled="showResult"
          placeholder="Введите правильный вариант."
          @keyup.enter="checkAnswer"
          class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-yellow-400 mb-4 text-lg"
        />

        <!-- РЕЗУЛЬТАТ -->
        <div v-if="showResult" class="text-center">
          <p v-if="isCorrect" class="text-green-600 font-semibold mb-2">✅ Верно!</p>
          <p v-else class="text-red-600 font-semibold mb-2">❌ Неверно. Правильно: <b>{{ currentTask.correct }}</b></p>

          <button
            @click="nextTask"
            class="mt-3 w-full bg-purple-600 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded-lg transition"
          >
            Следующий
          </button>
        </div>

        <!-- КНОПКА "ПРОВЕРИТЬ" -->
        <button
          v-else
          @click="checkAnswer"
          class="w-full bg-yellow-500 hover:bg-yellow-600 text-white font-bold py-2 px-4 rounded-lg transition"
        >
          Проверить
        </button>

        <!-- КНОПКИ НАЗАД / ВПЕРЁД -->
        <div class="flex justify-between mt-6">
          <button
            @click="prevTask"
            :disabled="currentIdx === 0"
            class="px-4 py-2 bg-gray-300 hover:bg-gray-400 disabled:bg-gray-200 disabled:text-gray-400 rounded-lg font-semibold transition"
          >
            ⬅️ Назад
          </button>

          <button
            @click="nextUnlocked"
            :disabled="currentIdx === tasks.length - 1"
            class="px-4 py-2 bg-gray-300 hover:bg-gray-400 disabled:bg-gray-200 disabled:text-gray-400 rounded-lg font-semibold transition"
          >
            Вперед ➡️
          </button>
        </div>

      </div>

      <!-- ФИНАЛ -->
      <div v-else class="text-center mt-6">
        <div class="bg-white rounded-xl shadow-lg p-6 inline-block">
          <p class="text-2xl font-bold text-purple-700 mb-2">🎉 Упражнение завершено!</p>
          <p class="text-lg mb-2">Ваш результат:</p>

          <p class="text-3xl font-extrabold text-green-600 mb-2">
            {{ correctCount }}/{{ tasks.length }} ({{ percent }}%)
          </p>

          <NuxtLink
            to="/"
            class="block text-blue-600 underline hover:text-blue-800 mt-2"
          >
            ⬅️ На главную
          </NuxtLink>
        </div>
      </div>

    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

interface Task {
  row: string
  correct: string
}

const tasks: Task[] = [
  { row: 'жАлюзи, языковОй (факт), позвонИшь, куУхонный', correct: 'жалюзИ' },
  { row: 'тОрты, красивЕе, позвонИшь, крапИва', correct: 'красИвее' },
  { row: 'договОр, пОняла, столЯр, алфавИт', correct: 'понялА' },
  { row: 'бАловать, вЕрба, позвонИшь, комбАйнер', correct: 'баловАть' },
  { row: 'завИдно, жалюзИ, каталОг, дремотА', correct: 'дремОта' },
  { row: 'приговОр, разОгнутый, газопровОд, рАкушка', correct: 'ракУшка' },
  { row: 'бантЫ, взялА, экспЕрт, чЕрпать', correct: 'бАнты' },
  { row: 'кружевА, агрономИя, зАговор, столЯр', correct: 'агронОмия' },
  { row: 'хозяевА, каучУк, ободрИть, житиЕ', correct: 'хозЯева' },
  { row: 'ветеринАрия, фарфОр, упрочЕние, взялА', correct: 'упрОчение' },
  { row: 'шАрфы, бытиЕ, подОгнутый, индУстрия', correct: 'индустрИя' },
  { row: 'досУг, боЯзнь, кладбИще, эксперт', correct: 'клАдбище' },
  { row: 'гУсеничный, трубопрОвод, врученА, киломЕтр', correct: 'трубопровОд' },
  { row: 'сливОвый, манЯщий, столЯр, диалОг', correct: 'слИвовый' },
  { row: 'рАзвитее, дефИс, Искра, тотчАс', correct: 'тОтчас' },
]

const currentIdx = ref(0)
const userAnswer = ref('')
const showResult = ref(false)
const isCorrect = ref(false)
const correctCount = ref(0)
const answered = ref<boolean[]>(Array(tasks.length).fill(false))

const currentTask = computed(() => tasks[currentIdx.value]!)
const finished = computed(() => currentIdx.value >= tasks.length)
const percent = computed(() => Math.round((correctCount.value / tasks.length) * 100))

function checkAnswer() {
  if (!userAnswer.value.trim()) return

  showResult.value = true
  isCorrect.value = userAnswer.value.trim().toLowerCase() === currentTask.value.correct.toLowerCase()

  answered.value[currentIdx.value] = true

  if (isCorrect.value) correctCount.value++
}

function nextTask() {
  currentIdx.value++
  userAnswer.value = ''
  showResult.value = false
  isCorrect.value = false
}

function prevTask() {
  if (currentIdx.value > 0) {
    currentIdx.value--
    userAnswer.value = ''
    showResult.value = false
    isCorrect.value = false
  }
}

function nextUnlocked() {
  if (currentIdx.value < tasks.length - 1) {
    currentIdx.value++
    userAnswer.value = ''
    showResult.value = false
    isCorrect.value = false
  }
}

function jumpTo(index: number) {
  currentIdx.value = index
  userAnswer.value = ''
  showResult.value = false
  isCorrect.value = false
}
</script>

<style scoped>
button {
  transition: all 0.2s ease-in-out;
}
button:hover:not(:disabled) {
  transform: scale(1.02);
}
</style>
