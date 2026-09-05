<template>
  <div class="relative min-h-screen bg-gradient-to-br from-pink-100 via-purple-50 to-blue-100 py-6 px-3 sm:py-8 sm:px-4">

    <!-- ОКОШКО НУМЕРАЦИИ -->
    <div
      v-if="!finished"
      class="mx-auto mt-4 w-full max-w-2xl rounded-xl border border-purple-300 bg-white p-3 shadow-xl sm:p-4 md:absolute md:right-4 md:top-4 md:mt-0 md:w-72"
    >
      <div class="grid grid-cols-5 gap-2 sm:gap-3">
        <button
          v-for="(q, i) in quiz"
          :key="i"
          @click="jumpTo(i)"
          class="flex h-9 w-full items-center justify-center rounded-md text-sm font-bold transition sm:h-10 sm:text-base"
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
    </div>

    <!-- ОСНОВНОЙ БЛОК -->
    <div class="mx-auto w-full max-w-2xl rounded-2xl border-t-4 border-pink-400 bg-white p-4 shadow-2xl sm:p-6 md:p-8">

      <h2 class="mb-2 text-center text-2xl font-bold text-purple-700 sm:text-3xl">
        🎯 Викторина: Ударения
      </h2>

      <p class="mb-5 text-center text-sm text-gray-500 sm:mb-6 sm:text-base">
        Выберите правильный вариант ударения
      </p>

      <!-- ВИКТОРИНА -->
      <div v-if="!finished || showPaw">

        <div class="mb-4 text-center text-base font-semibold text-blue-700 sm:text-lg">
          Вопрос {{ currentIdx + 1 }} из {{ quiz.length }}
        </div>

        <div class="mb-5 break-words text-center text-2xl font-bold text-purple-700 sm:mb-6 sm:text-3xl">
          {{ currentQuestion.word }}
        </div>

        <!-- ВАРИАНТЫ -->
        <div class="mb-6 flex flex-col gap-3 sm:gap-4">
          <button
            v-for="(option, idx) in currentQuestion.options"
            :key="idx"
            :disabled="showResult"
            @click="selectOption(idx)"
            class="relative w-full min-w-0 min-h-[54px] overflow-hidden rounded-xl border py-3 pl-4 pr-16 text-left text-base font-medium transition sm:text-lg"
            :class="[
              selectedIdx === idx
                ? 'bg-blue-100 border-blue-400'
                : 'bg-white border-gray-300',

              showResult && idx === currentQuestion.correct
                ? 'bg-green-100 border-green-500 shadow-md'
                : '',

              showResult &&
              selectedIdx === idx &&
              selectedIdx !== currentQuestion.correct
                ? 'bg-red-100 border-red-500 shadow-md'
                : ''
            ]"
          >
            <span class="block break-words whitespace-normal leading-relaxed [overflow-wrap:anywhere]">
              {{ option }}
            </span>

            <!-- ЛЕОПАРДОВАЯ ЛАПКА -->
            <Transition name="paw">
              <div
                v-if="
                  showPaw &&
                  idx === currentQuestion.correct &&
                  selectedIdx === currentQuestion.correct
                "
                class="pointer-events-none absolute right-1 top-1/2 z-20 -translate-y-1/2 sm:right-2"
                aria-hidden="true"
              >
                <svg
                  viewBox="0 0 90 90"
                  class="paw-svg"
                  xmlns="http://www.w3.org/2000/svg"
                >
                  <defs>
                    <radialGradient id="furGradientQuiz" cx="35%" cy="25%" r="80%">
                      <stop offset="0%" stop-color="#f8ddb0"/>
                      <stop offset="55%" stop-color="#e5b978"/>
                      <stop offset="100%" stop-color="#b9783e"/>
                    </radialGradient>

                    <radialGradient id="padGradientQuiz" cx="35%" cy="30%" r="75%">
                      <stop offset="0%" stop-color="#ffe5e8"/>
                      <stop offset="60%" stop-color="#f5b5c1"/>
                      <stop offset="100%" stop-color="#d98294"/>
                    </radialGradient>

                    <filter id="pawShadowQuiz" x="-30%" y="-30%" width="160%" height="160%">
                      <feDropShadow
                        dx="1"
                        dy="3"
                        stdDeviation="2"
                        flood-opacity="0.28"
                      />
                    </filter>
                  </defs>

                  <!-- ПАЛЬЧИКИ -->
                  <g
                    fill="url(#furGradientQuiz)"
                    stroke="#8b572f"
                    stroke-width="1.2"
                    filter="url(#pawShadowQuiz)"
                  >
                    <ellipse cx="22" cy="24" rx="9" ry="14" transform="rotate(-25 22 24)"/>
                    <ellipse cx="39" cy="17" rx="9" ry="14" transform="rotate(-8 39 17)"/>
                    <ellipse cx="57" cy="19" rx="9" ry="14" transform="rotate(12 57 19)"/>
                    <ellipse cx="71" cy="29" rx="8" ry="13" transform="rotate(27 71 29)"/>

                    <!-- ЛАДОНЬ -->
                    <ellipse cx="47" cy="54" rx="25" ry="28"/>
                  </g>

                  <!-- ПОДУШЕЧКИ -->
                  <g
                    fill="url(#padGradientQuiz)"
                    stroke="#b86d7d"
                    stroke-width="1"
                  >
                    <ellipse cx="22" cy="25" rx="5" ry="7"/>
                    <ellipse cx="39" cy="18" rx="5" ry="7"/>
                    <ellipse cx="57" cy="20" rx="5" ry="7"/>
                    <ellipse cx="70" cy="30" rx="4.5" ry="6.5"/>
                    <ellipse cx="47" cy="55" rx="14" ry="17"/>
                  </g>

                  <!-- ПЯТНЫШКИ ЛЕОПАРДА -->
                  <g fill="#7b4a2c" opacity="0.9">
                    <circle cx="15" cy="18" r="2.2"/>
                    <circle cx="28" cy="14" r="2.5"/>
                    <circle cx="35" cy="28" r="2"/>
                    <circle cx="48" cy="10" r="2.2"/>
                    <circle cx="63" cy="14" r="2.4"/>
                    <circle cx="76" cy="24" r="2"/>
                    <circle cx="19" cy="42" r="2.5"/>
                    <circle cx="31" cy="49" r="2"/>
                    <circle cx="63" cy="43" r="2.3"/>
                    <circle cx="72" cy="55" r="2"/>
                    <circle cx="35" cy="69" r="2.3"/>
                    <circle cx="58" cy="72" r="2.5"/>
                  </g>

                  <!-- БЛИК -->
                  <ellipse
                    cx="39"
                    cy="48"
                    rx="6"
                    ry="9"
                    fill="#fff"
                    opacity="0.18"
                  />
                </svg>

                <span class="paw-spark spark-one">✦</span>
                <span class="paw-spark spark-two">•</span>
                <span class="paw-spark spark-three">✦</span>
              </div>
            </Transition>
          </button>
        </div>

        <!-- РЕЗУЛЬТАТ ОТВЕТА -->
        <div
          v-if="showResult"
          class="mt-4 flex flex-col gap-4 text-center"
        >
          <div>
            <p
              v-if="selectedIdx === currentQuestion.correct"
              class="mb-2 font-semibold text-green-600"
            >
              ✅ Правильно!
            </p>

            <p
              v-else
              class="mb-2 font-semibold text-red-600"
            >
              ❌ Неверно. Правильный ответ:
              <b>
                {{ currentQuestion.options[currentQuestion.correct] }}
              </b>
            </p>

            <p class="mb-3 mt-3 break-words text-sm text-gray-700 sm:text-base">
              📘 <span class="italic">{{ currentQuestion.rule }}</span>
            </p>

            <audio
              v-if="currentQuestion.audio"
              :src="currentQuestion.audio"
              controls
              class="mx-auto my-2 max-w-full"
            ></audio>
          </div>

          <!-- КНОПКИ НАВИГАЦИИ -->
          <div class="flex flex-col gap-3 sm:flex-row sm:justify-between sm:gap-4">

            <button
              @click="prevQuestion"
              :disabled="currentIdx === 0"
              class="w-full rounded-lg bg-gray-300 px-4 py-3 font-bold text-gray-800 transition hover:bg-gray-400 disabled:opacity-50 sm:flex-1"
            >
              ⬅️ Назад
            </button>

            <button
              @click="nextQuestion"
              :disabled="currentIdx >= quiz.length - 1"
              class="w-full rounded-lg bg-purple-600 px-4 py-3 font-bold text-white transition hover:bg-purple-700 disabled:opacity-50 sm:flex-1"
            >
              Вперёд ➡️
            </button>

          </div>
        </div>

      </div>

      <!-- ФИНАЛЬНЫЙ РЕЗУЛЬТАТ -->
      <div v-else class="mt-6 text-center">

        <div class="rounded-2xl bg-purple-50 p-4 shadow-lg sm:p-8">

          <p class="mb-3 text-2xl font-extrabold text-purple-700 sm:text-3xl">
            🎉 Ваши результаты
          </p>

          <p class="mb-6 text-base text-gray-600 sm:text-lg">
            Вы выполнили задание «Викторина»
          </p>

          <div class="mb-5 rounded-xl bg-white p-5 shadow-md sm:p-6">

            <p class="mb-2 text-gray-600">
              Результат
            </p>

            <p class="mb-3 text-4xl font-extrabold text-green-600">
              {{ correctCount }}/{{ quiz.length }}
            </p>

            <p class="mb-3 text-2xl font-bold">
              {{ percent }}%
            </p>

            <p class="text-xl font-semibold">
              Оценка:
              <span :class="gradeColor">
                {{ grade }}
              </span>
            </p>

          </div>

          <!-- СОХРАНЕНИЕ -->
          <p
            v-if="saveStatus === 'saving'"
            class="mb-4 text-blue-600"
          >
            Сохраняем результат...
          </p>

          <p
            v-if="saveStatus === 'saved'"
            class="mb-4 font-semibold text-green-600"
          >
            ✅ Результат сохранён
          </p>

          <p
            v-if="saveStatus === 'error'"
            class="mb-4 font-semibold text-red-600"
          >
            ❌ Не удалось сохранить результат
          </p>

          <!-- КНОПКИ -->
          <div class="mt-5 flex flex-col gap-3">

            <NuxtLink
              to="/results"
              class="block rounded-xl bg-purple-600 px-5 py-3 font-semibold text-white shadow-md transition hover:bg-purple-700"
            >
              📊 Мой прогресс
            </NuxtLink>

            <NuxtLink
              to="/"
              class="block rounded-xl bg-gray-300 px-5 py-3 font-semibold text-gray-800 transition hover:bg-gray-400"
            >
              ⬅️ На главную
            </NuxtLink>

          </div>

        </div>

      </div>

    </div>

  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'

interface Question {
  word: string
  options: string[]
  correct: number
  rule: string
  audio?: string
}

const supabase = useSupabaseClient()

const quiz: Question[] = [
  { word: 'каталог', options: ['катАлог', 'каталОг'], correct: 1, rule: 'В словах на -лог ударение падает на последний слог.', audio: '/audio/katalog.ogg' },
  { word: 'свёкла', options: ['свеклА', 'свЁкла'], correct: 1, rule: 'Ударение на первом слоге.', audio: '/audio/svekla.ogg' },
  { word: 'тортов', options: ['тОртов', 'тортОв'], correct: 0, rule: 'Ударение сохраняется на основе.', audio: '/audio/tortov.ogg' },
  { word: 'заняла', options: ['занялА', 'зАняла'], correct: 0, rule: 'Ударение в ж.р. на окончание.', audio: '/audio/zanyala.ogg' },
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

const userAnswers = ref<(number | null)[]>(
  Array(quiz.length).fill(null)
)

const answered = ref<boolean[]>(
  Array(quiz.length).fill(false)
)

const showPaw = ref(false)

let pawTimer: ReturnType<typeof setTimeout> | null = null

const studentName = ref('')
const studentClass = ref('')
const startedAt = ref('')
const attemptId = ref<string | null>(null)

const saveStatus = ref<'saving' | 'saved' | 'error' | null>(null)

const currentQuestion = computed(() => quiz[currentIdx.value]!)

const finished = computed(() =>
  answered.value.every(a => a)
)

const percent = computed(() =>
  Math.round((correctCount.value / quiz.length) * 100)
)

const grade = computed(() => {
  if (percent.value < 50) return '2'
  if (percent.value <= 70) return '3'
  if (percent.value <= 84) return '4'
  return '5'
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

onMounted(async () => {
  const savedStudent = localStorage.getItem('orphoepia_student')

  if (!savedStudent) {
    await navigateTo('/')
    return
  }

  try {
    const student = JSON.parse(savedStudent)

    studentName.value = student.name || ''
    studentClass.value = student.class || ''

    if (!studentName.value || !studentClass.value) {
      await navigateTo('/')
      return
    }
  } catch {
    await navigateTo('/')
    return
  }

  startedAt.value = new Date().toISOString()

  const { data, error } = await supabase
    .from('attempts')
    .insert({
      student_name: studentName.value,
      class: studentClass.value,
      task_name: 'Викторина',
      total_questions: quiz.length,
      started_at: startedAt.value
    })
    .select('id')
    .single()

  if (error) {
    console.error('Ошибка создания попытки:', error)
    saveStatus.value = 'error'
    return
  }

  attemptId.value = data.id
})

function triggerCorrectPaw() {
  if (pawTimer) {
    clearTimeout(pawTimer)
  }

  showPaw.value = true

  pawTimer = setTimeout(() => {
    showPaw.value = false
    pawTimer = null
  }, 3000)
}

function selectOption(idx: number) {
  if (showResult.value) return

  selectedIdx.value = idx
  userAnswers.value[currentIdx.value] = idx
  showResult.value = true

  answered.value[currentIdx.value] = true

  if (idx === currentQuestion.value.correct) {
    correctCount.value++
    triggerCorrectPaw()
  }

  if (finished.value) {
    saveResult()
  }
}

async function saveResult() {
  if (!attemptId.value) {
    saveStatus.value = 'error'
    return
  }

  saveStatus.value = 'saving'

  const completedAt = new Date().toISOString()

  const { error } = await supabase
    .from('attempts')
    .update({
      score: correctCount.value,
      percentage: percent.value,
      completed_at: completedAt
    })
    .eq('id', attemptId.value)

  if (error) {
    console.error('Ошибка сохранения результата:', error)
    saveStatus.value = 'error'
    return
  }

  saveStatus.value = 'saved'
}

function nextQuestion() {
  if (currentIdx.value < quiz.length - 1) {
    showPaw.value = false
    currentIdx.value++
  }

  selectedIdx.value = userAnswers.value[currentIdx.value]
  showResult.value = answered.value[currentIdx.value]
}

function prevQuestion() {
  if (currentIdx.value > 0) {
    showPaw.value = false
    currentIdx.value--
  }

  selectedIdx.value = userAnswers.value[currentIdx.value]
  showResult.value = answered.value[currentIdx.value]
}

function jumpTo(i: number) {
  showPaw.value = false

  currentIdx.value = i
  selectedIdx.value = userAnswers.value[i]
  showResult.value = answered.value[i]
}

onBeforeUnmount(() => {
  if (pawTimer) {
    clearTimeout(pawTimer)
  }
})
</script>

<style scoped>
button {
  transition: all 0.2s ease-in-out;
}

button:hover:not(:disabled) {
  transform: scale(1.02);
}

.paw-svg {
  width: 48px;
  height: 48px;
  display: block;
  transform-origin: 70% 80%;
  animation: paw-life 1.1s ease-in-out infinite;
}

.paw-spark {
  position: absolute;
  pointer-events: none;
  color: #d79a4d;
  font-weight: 900;
  text-shadow: 0 1px 3px rgba(130, 75, 30, 0.2);
}

.spark-one {
  top: 3px;
  right: 2px;
  font-size: 12px;
  animation: sparkle-one 1s ease-in-out infinite;
}

.spark-two {
  top: 18px;
  right: -2px;
  font-size: 9px;
  animation: sparkle-two 1.2s ease-in-out infinite;
}

.spark-three {
  bottom: 5px;
  left: 1px;
  font-size: 10px;
  animation: sparkle-three 1.1s ease-in-out infinite;
}

.paw-enter-active,
.paw-leave-active {
  transition:
    opacity 0.45s ease,
    transform 0.45s cubic-bezier(0.2, 0.8, 0.2, 1);
}

.paw-enter-from {
  opacity: 0;
  transform: translate(18px, -50%) rotate(18deg) scale(0.65);
}

.paw-enter-to {
  opacity: 1;
  transform: translate(0, -50%) rotate(0deg) scale(1);
}

.paw-leave-from {
  opacity: 1;
  transform: translate(0, -50%) rotate(0deg) scale(1);
}

.paw-leave-to {
  opacity: 0;
  transform: translate(18px, -50%) rotate(14deg) scale(0.7);
}

@keyframes paw-life {
  0%,
  100% {
    transform: rotate(-3deg) translateY(0);
  }

  50% {
    transform: rotate(4deg) translateY(-2px);
  }
}

@keyframes sparkle-one {
  0%,
  100% {
    opacity: 0.25;
    transform: scale(0.8);
  }

  50% {
    opacity: 1;
    transform: scale(1.15);
  }
}

@keyframes sparkle-two {
  0%,
  100% {
    opacity: 0.2;
    transform: translateY(1px);
  }

  50% {
    opacity: 0.9;
    transform: translateY(-3px);
  }
}

@keyframes sparkle-three {
  0%,
  100% {
    opacity: 0.25;
    transform: scale(0.8);
  }

  50% {
    opacity: 1;
    transform: scale(1.1);
  }
}

@media (max-width: 360px) {
  .paw-svg {
    width: 44px;
    height: 44px;
  }
}
</style>
