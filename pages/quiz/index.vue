<template>
  <div class="relative min-h-screen flex items-center justify-center bg-gradient-to-br from-pink-100 via-purple-50 to-blue-100 py-8">

    <!-- 🟪 ОКОШКО НУМЕРАЦИИ -->
    <div
      class="absolute top-4 right-4 bg-white shadow-xl rounded-xl p-4 w-72 grid grid-cols-5 gap-3 border border-purple-300"
    >
      <button
        v-for="(q, i) in quiz"
        :key="i"
        @click="jumpTo(i)"
        class="w-10 h-10 flex items-center justify-center rounded-md text-base font-bold transition"
        :class="[
          currentIdx === i
            ? 'bg-purple-300 text-white'
            : answered[i]
              ? 'bg-green-300 text-white'
              : 'bg-gray-200 text-gray-600 hover:bg-gray-300'
        ]"
      >
        {{ i + 1 }}
      </button>
    </div>

    <!-- ОСНОВНОЙ БЛОК -->
    <div class="w-full max-w-2xl bg-white rounded-2xl shadow-2xl p-8 border-t-4 border-pink-400">
      <h2 class="text-3xl font-bold text-center mb-2 text-purple-700">🎯 Викторина: Ударения</h2>
      <p class="text-center text-gray-500 mb-6">
        Выберите правильный вариант ударения
      </p>

      <!-- Основная часть -->
      <div v-if="!finished">

        <div class="mb-4 text-lg font-semibold text-center text-blue-700">
          Вопрос {{ currentIdx + 1 }} из {{ quiz.length }}
        </div>

        <div class="text-center text-2xl font-bold text-purple-700 mb-6">
          {{ currentQuestion.word }}
        </div>

        <div class="flex flex-col gap-4 mb-6">
          <button
            v-for="(option, idx) in currentQuestion.options"
            :key="idx"
            :class="[ 
              'py-3 px-4 rounded-lg border text-lg font-medium transition transform hover:scale-[1.02]',
              selectedIdx === idx ? 'bg-blue-100 border-blue-400' : 'bg-white border-gray-300',
              showResult && idx === currentQuestion.correct ? 'bg-green-100 border-green-500 shadow-md' : '',
              showResult && selectedIdx === idx && selectedIdx !== currentQuestion.correct ? 'bg-red-100 border-red-500 shadow-md' : ''
            ]"
            @click="selectOption(idx)"
            :disabled="showResult"
          >
            {{ option }}
          </button>
        </div>

        <!-- Результат + кнопки Назад/Вперёд -->
        <div v-if="showResult" class="text-center mt-4 flex flex-col gap-4">

          <div>
            <p v-if="selectedIdx === currentQuestion.correct" class="text-green-600 font-semibold mb-2">
              ✅ Правильно!
            </p>
            <p v-else class="text-red-600 font-semibold mb-2">
              ❌ Неверно. Правильный ответ:
              <b>{{ currentQuestion.options[currentQuestion.correct] }}</b>
            </p>
            <p class="text-gray-700 mt-3 mb-3">
              📘 <span class="italic">{{ currentQuestion.rule }}</span>
            </p>

            <audio
              v-if="currentQuestion.audio"
              :src="currentQuestion.audio"
              controls
              class="mx-auto my-2"
            ></audio>
          </div>

          <div class="flex justify-between gap-4">
            <button
              @click="prevQuestion"
              :disabled="currentIdx === 0"
              class="flex-1 bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded-lg transition disabled:opacity-50"
            >
              ⬅️ Назад
            </button>

            <button
              @click="nextQuestion"
              :disabled="currentIdx >= quiz.length - 1"
              class="flex-1 bg-purple-600 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded-lg transition disabled:opacity-50"
            >
              Вперёд ➡️
            </button>
          </div>
        </div>

      </div>

      <!-- Финал -->
      <div v-else class="text-center mt-6">
        <div class="bg-white rounded-xl shadow-lg p-6 inline-block">
          <p class="text-2xl font-bold text-purple-700 mb-2">🎉 Викторина завершена!</p>
          <p class="text-lg mb-2">Ваш результат:</p>

          <p class="text-3xl font-extrabold text-green-600 mb-2">
            {{ correctCount }}/{{ quiz.length }} ({{ percent }}%)
          </p>

          <p class="text-xl font-semibold mb-4">
            Оценка:
            <span :class="gradeColor">{{ grade }}</span>
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

interface Question {
  word: string
  options: string[]
  correct: number
  rule: string
  audio?: string
}

const quiz: Question[] = [
  { word: 'каталог', options: ['катАлог', 'каталОг'], correct: 1, rule: 'В словах на -лог ударение падает на последний слог.', audio: '/audio/katalog.ogg' },
  { word: 'свёкла', options: ['свеклА', 'свЁкла'], correct: 1, rule: 'Ударение на первом слоге.', audio: '/audio/svekla.ogg' },
  { word: 'тортов', options: ['тОртов', 'тортОв'], correct: 0, rule: 'Ударение сохраняется на основе.' , audio: '/audio/tortov.ogg'},
  { word: 'заняла', options: ['занялА', 'зАняла'], correct: 0, rule: 'Ударение в ж.р. на окончание.' , audio: '/audio/zanyala.ogg'},
  { word: 'щавель', options: ['щАвель', 'щавЕль'], correct: 1, rule: 'Ударение на последний слог.', audio: '/audio/shavel.ogg' },
  { word: 'квартал', options: ['квАртал', 'квартАл'], correct: 1, rule: 'Ударение в -ал на последний слог.', audio: '/audio/kvartal.ogg' },
  { word: 'кремень', options: ['крЕмень', 'кремЕнь'], correct: 1, rule: 'Ударение в -ень на последний слог.', audio: '/audio/kremen.ogg' },
  { word: 'жалюзи', options: ['жАлюзи', 'жалюзИ'], correct: 1, rule: 'Французские слова — ударение на конце.', audio: '/audio/zhaluzi.ogg' },
  { word: 'позвонишь', options: ['позвОнишь', 'позвонИшь'], correct: 1, rule: 'Ударение в глаголах на окончание.', audio: '/audio/pozvovish.ogg' },
  { word: 'красивее', options: ['крАсивее', 'красИвее'], correct: 1, rule: 'Ударение на -и- в сравнительной степени.', audio: '/audio/krasivee.ogg' },
  { word: 'начав', options: ['нАчав', 'начАв'], correct: 1, rule: 'В деепричастиях ударение на окончание.', audio: '/audio/nachav.ogg' },
  { word: 'баловать', options: ['бАловать', 'баловАть'], correct: 1, rule: 'В -овать ударение на окончание.', audio: '/audio/balovat.ogg' },
  { word: 'краны', options: ['крАны', 'кранЫ'], correct: 0, rule: 'В муж. р. ударение на первом слоге.', audio: '/audio/krany.ogg' },
  { word: 'включим', options: ['вклЮчим', 'включИм'], correct: 1, rule: 'Ударение на окончание.', audio: '/audio/vkluchim.ogg' },
  { word: 'создала', options: ['сОздала', 'создалА'], correct: 1, rule: 'В ж.р. ударение на окончание.', audio: '/audio/sozdala.ogg' },
]

const currentIdx = ref(0)
const selectedIdx = ref<number | null>(null)
const showResult = ref(false)
const correctCount = ref(0)
const answered = ref<boolean[]>(Array(quiz.length).fill(false))

const currentQuestion = computed(() => quiz[currentIdx.value]!)
const finished = computed(() => answered.value.every(a => a))
const percent = computed(() => Math.round((correctCount.value / quiz.length) * 100))

function selectOption(idx: number) {
  if (showResult.value) return
  selectedIdx.value = idx
  showResult.value = true
  answered.value[currentIdx.value] = true
  if (idx === currentQuestion.value.correct) correctCount.value++
}

function nextQuestion() {
  if (currentIdx.value < quiz.length - 1) currentIdx.value++
  selectedIdx.value = answered.value[currentIdx.value] ? quiz[currentIdx.value].correct : null
  showResult.value = answered.value[currentIdx.value]
}

function prevQuestion() {
  if (currentIdx.value > 0) currentIdx.value--
  selectedIdx.value = answered.value[currentIdx.value] ? quiz[currentIdx.value].correct : null
  showResult.value = answered.value[currentIdx.value]
}

function jumpTo(i: number) {
  currentIdx.value = i
  selectedIdx.value = answered.value[i] ? quiz[i].correct : null
  showResult.value = answered.value[i]
}

const grade = computed(() => {
  if (percent.value < 50) return '2'
  if (percent.value <= 70) return '3'
  if (percent.value <= 84) return '4'
  if (percent.value >= 85) return '5'
  return '-'
})

const gradeColor = computed(() => {
  return grade.value === '5'
    ? 'text-green-600'
    : grade.value === '4'
      ? 'text-blue-600'
      : grade.value === '3'
        ? 'text-yellow-600'
        : 'text-red-600'
})
</script>

<style scoped>
button {
  transition: all 0.2s ease-in-out;
}
button:hover:not(:disabled) {
  transform: scale(1.02);
}
</style>
