<template>
  <div class="min-h-screen relative bg-gradient-to-br from-blue-100 via-purple-50 to-pink-100 py-8 flex items-center justify-center">

    <!-- 🔷 Окошко с номерами вопросов -->
    <div
      class="absolute top-4 right-4 grid grid-cols-5 gap-2 p-3 bg-white rounded-xl shadow-lg border border-purple-200 w-72"
    >
      <div
        v-for="(q, index) in questions"
        :key="index"
        @click="jumpTo(index)"
        class="w-10 h-10 flex items-center justify-center rounded-lg cursor-pointer border font-semibold text-base transition"
        :class="[
          currentIdx === index
            ? 'bg-blue-200 border-blue-500 text-blue-800'
            : answeredQuestions[index]
              ? 'bg-green-200 border-green-500 text-green-800'
              : 'bg-white border-gray-300 text-gray-700 hover:bg-gray-100'
        ]"
      >
        {{ index + 1 }}
      </div>
    </div>

    <!-- 🟣 Основной блок -->
    <div class="w-full max-w-2xl bg-white rounded-2xl shadow-2xl p-8 border-t-4 border-purple-400">
      <h2 class="text-3xl font-bold text-center mb-2 text-purple-700">🗣️Укажи верное ударение</h2>
      <p class="text-center text-gray-500 mb-6">
        Прочитайте предложение и укажите, верно ли поставлено ударение.
      </p>

      <div v-if="!finished">
        <div class="mb-4 text-lg font-semibold text-center text-blue-700">
          Вопрос {{ currentIdx + 1 }} из {{ questions.length }}
        </div>

        <p class="text-center text-xl mb-6">
          <span v-html="currentQuestion.sentence" class="font-medium"></span>
        </p>

        <div class="flex flex-col gap-3 mb-6">
          <button
            v-for="(option, idx) in ['Верно', 'Неверно']"
            :key="idx"
            :class="[
              'py-3 px-4 rounded-lg border text-lg font-medium transition transform hover:scale-[1.02]',
              selectedIdx === idx ? 'bg-blue-100 border-blue-400' : 'bg-white border-gray-300',
              showResult && idx === currentQuestion.correct ? 'bg-green-100 border-green-500 shadow-md' : '',
              showResult && selectedIdx === idx && selectedIdx !== currentQuestion.correct ? 'bg-red-100 border-red-500 shadow-md' : ''
            ]"
            @click="selectOption(idx)"
          >
            {{ option }}
          </button>
        </div>

        <div v-if="showResult" class="text-center">
          <p v-if="selectedIdx === currentQuestion.correct" class="text-green-600 font-semibold mb-2">
            ✅ Правильно!
          </p>
          <p v-else class="text-red-600 font-semibold mb-2">
            ❌ Неверно. Правильно: <b>{{ currentQuestion.correctAnswer }}</b>
          </p>

          <audio
            v-if="currentQuestion.audio"
            ref="audioRef"
            :src="currentQuestion.audio"
            controls
            class="mx-auto my-2"
          ></audio>

          <div class="flex justify-between gap-4 mt-3">
            <button
              @click="prevQuestion"
              :disabled="currentIdx === 0"
              class="flex-1 bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded-lg transition disabled:opacity-50"
            >
              ⬅️ Назад
            </button>
            <button
              @click="nextQuestion"
              :disabled="currentIdx >= questions.length - 1"
              class="flex-1 bg-purple-600 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded-lg transition disabled:opacity-50"
            >
              Вперёд ➡️
            </button>
          </div>
        </div>
      </div>

      <div v-else class="text-center mt-6">
        <div class="bg-white rounded-xl shadow-lg p-6 inline-block">
          <p class="text-2xl font-bold text-purple-700 mb-2">🎉 Упражнение завершено!</p>
          <p class="text-lg mb-2">Ваш результат:</p>
          <p class="text-3xl font-extrabold text-green-600 mb-2">{{ correctCount }}/{{ questions.length }} ({{ percent }}%)</p>

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
import { ref, computed, nextTick, watch } from 'vue'

interface Question {
  sentence: string
  correct: number
  correctAnswer: string
  audio?: string
}

const questions: Question[] = [
  { sentence: 'Учитель сделал ему строгие <b>вЫговоры</b> за небрежное отношение к учёбе.', correct: 0, correctAnswer: 'верно', audio: '/audio/vygovory.ogg' },
  { sentence: 'Солнце падало <b>нАискось</b>, освещая пыльную дорогу.', correct: 0, correctAnswer: 'нАискось', audio: '/audio/naiskos.ogg' },
  { sentence: 'После университета он поступил на факультет <b>агрономИи</b>.', correct: 1, correctAnswer: 'агронОмии', audio: '/audio/agronomii.ogg' },
  { sentence: 'Судьба свела её с настоящим <b>избрАнником</b> сердца.', correct: 0, correctAnswer: 'верно', audio: '/audio/izbrannik.ogg' },
  { sentence: 'Этот обычай существует <b>издавнА</b> и передаётся из поколения в поколение.', correct: 1, correctAnswer: 'ИздавнА', audio: '/audio/izdavna.ogg' },
  { sentence: '<b>АнатОм</b> внимательно рассматривал строение тела.', correct: 1, correctAnswer: 'анАтом', audio: '/audio/anatom.ogg' },
  { sentence: 'Он ударил <b>наОтмашь</b>, не рассчитав силы.', correct: 0, correctAnswer: 'верно', audio: '/audio/naotmash.ogg' },
  { sentence: 'Ребёнок осторожно <b>черпАет</b> воду из ведра.', correct: 1, correctAnswer: 'чЕрпает', audio: '/audio/cherpaet.ogg' },
  { sentence: 'Учитель говорил об <b>обеспечЕнии</b> безопасности.', correct: 1, correctAnswer: 'обеспЕчении', audio: '/audio/obespechenie.ogg' },
  { sentence: 'На платье были красивые <b>кружевА</b> ручной работы.', correct: 0, correctAnswer: 'верно', audio: '/audio/kruzheva.ogg' },
  { sentence: 'В доме отремонтировали <b>мусоропровОд</b>.', correct: 0, correctAnswer: 'верно', audio: '/audio/musoroprovod.ogg' },
  { sentence: 'Звук бормашины <b>свЕрлит</b> ухо до боли.', correct: 1, correctAnswer: 'сверлИт', audio: '/audio/sverl.ogg' },
  { sentence: 'Зуб сильно болел, и врач начал его <b>пломбирОвать</b>.', correct: 1, correctAnswer: 'пломбировАть', audio: '/audio/plombirovat.ogg' },
  { sentence: 'В его глазах сверкнула <b>Искра</b> вдохновения.', correct: 0, correctAnswer: 'верно', audio: '/audio/iskra.ogg' },
  { sentence: 'Учителя решили <b>премИровать</b> лучших учеников.', correct: 1, correctAnswer: 'премировАть', audio: '/audio/premirovat.ogg' },
]

const currentIdx = ref(0)
const selectedIdx = ref<number | null>(null)
const showResult = ref(false)
const correctCount = ref(0)
const audioRef = ref<HTMLAudioElement | null>(null)
const answeredQuestions = ref<boolean[]>(questions.map(() => false))

const currentQuestion = computed(() => questions[currentIdx.value]!)
const finished = computed(() => answeredQuestions.value.every(a => a))
const percent = computed(() => Math.round((correctCount.value / questions.length) * 100))

function selectOption(idx: number) {
  if (showResult.value) return
  selectedIdx.value = idx
  showResult.value = true
  if (idx === currentQuestion.value.correct) correctCount.value++
  answeredQuestions.value[currentIdx.value] = true
}

watch(showResult, async (val) => {
  if (val && currentQuestion.value.audio) {
    await nextTick()
    audioRef.value?.play().catch(() => {})
  }
})

function nextQuestion() {
  if (currentIdx.value < questions.length - 1) currentIdx.value++
  selectedIdx.value = answeredQuestions.value[currentIdx.value] ? 0 : null
  showResult.value = answeredQuestions.value[currentIdx.value]
}

function prevQuestion() {
  if (currentIdx.value > 0) currentIdx.value--
  selectedIdx.value = answeredQuestions.value[currentIdx.value] ? 0 : null
  showResult.value = answeredQuestions.value[currentIdx.value]
}

function jumpTo(index: number) {
  currentIdx.value = index
  selectedIdx.value = answeredQuestions.value[index] ? 0 : null
  showResult.value = answeredQuestions.value[index]
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
