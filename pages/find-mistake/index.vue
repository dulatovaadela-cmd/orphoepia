<template>
  <div
    class="min-h-screen relative bg-gradient-to-br from-yellow-100 via-pink-50 to-purple-100 py-6 px-3 sm:py-8 sm:px-4"
  >
    <!-- ОСНОВНОЙ БЛОК -->
    <div
      v-if="!finished"
      class="mx-auto w-full max-w-2xl bg-white rounded-2xl shadow-2xl p-4 sm:p-8 border-t-4 border-yellow-400"
    >
      <h2
        class="text-2xl sm:text-3xl font-bold text-center mb-2 text-purple-700 break-words"
      >
        ✍️ Найди ошибку в ударении
      </h2>

      <p
        class="text-center text-gray-500 mb-6 text-sm sm:text-base leading-relaxed break-words"
      >
        В каждом ряду одно слово с неправильным ударением. Введите его правильно.
      </p>

      <div>
        <div
          class="mb-4 text-base sm:text-lg font-semibold text-center text-yellow-700"
        >
          Вопрос {{ currentIdx + 1 }} из {{ tasks.length }}
        </div>

        <div
          class="text-center text-lg sm:text-xl font-medium text-purple-800 mb-6 leading-relaxed break-words [overflow-wrap:anywhere]"
        >
          {{ currentTask.row }}
        </div>

        <!-- ПОЛЕ ОТВЕТА -->
        <div class="relative mb-4">
          <input
            v-model="userAnswer"
            :disabled="showResult"
            placeholder="Введите правильный вариант"
            @keyup.enter="checkAnswer"
            class="w-full min-w-0 px-4 py-3 pr-16 border border-gray-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-yellow-400 text-base sm:text-lg"
          />

          <!-- ЛАПКА -->
          <Transition name="paw">
            <div
              v-if="showPaw && isCorrect"
              class="pointer-events-none absolute right-1 sm:right-2 top-1/2 z-20 -translate-y-1/2"
              aria-hidden="true"
            >
              <div class="relative">
                <svg
                  viewBox="0 0 90 90"
                  class="paw-svg"
                  xmlns="http://www.w3.org/2000/svg"
                >
                  <defs>
                    <radialGradient
                      id="furGradientFindMistake"
                      cx="35%"
                      cy="25%"
                      r="80%"
                    >
                      <stop offset="0%" stop-color="#f8ddb0" />
                      <stop offset="55%" stop-color="#e5b978" />
                      <stop offset="100%" stop-color="#b9783e" />
                    </radialGradient>

                    <radialGradient
                      id="padGradientFindMistake"
                      cx="35%"
                      cy="30%"
                      r="75%"
                    >
                      <stop offset="0%" stop-color="#ffe5e8" />
                      <stop offset="60%" stop-color="#f5b5c1" />
                      <stop offset="100%" stop-color="#d98294" />
                    </radialGradient>

                    <filter
                      id="pawShadowFindMistake"
                      x="-30%"
                      y="-30%"
                      width="160%"
                      height="160%"
                    >
                      <feDropShadow
                        dx="1"
                        dy="3"
                        stdDeviation="2"
                        flood-opacity="0.28"
                      />
                    </filter>
                  </defs>

                  <!-- ЛАПКА -->
                  <g
                    fill="url(#furGradientFindMistake)"
                    stroke="#8b572f"
                    stroke-width="1.2"
                    filter="url(#pawShadowFindMistake)"
                  >
                    <ellipse
                      cx="22"
                      cy="24"
                      rx="9"
                      ry="14"
                      transform="rotate(-25 22 24)"
                    />
                    <ellipse
                      cx="39"
                      cy="17"
                      rx="9"
                      ry="14"
                      transform="rotate(-8 39 17)"
                    />
                    <ellipse
                      cx="57"
                      cy="19"
                      rx="9"
                      ry="14"
                      transform="rotate(12 57 19)"
                    />
                    <ellipse
                      cx="71"
                      cy="29"
                      rx="8"
                      ry="13"
                      transform="rotate(27 71 29)"
                    />
                    <ellipse cx="47" cy="54" rx="25" ry="28" />
                  </g>

                  <!-- РОЗОВЫЕ ПОДУШЕЧКИ -->
                  <g
                    fill="url(#padGradientFindMistake)"
                    stroke="#b86d7d"
                    stroke-width="1"
                  >
                    <ellipse cx="22" cy="25" rx="5" ry="7" />
                    <ellipse cx="39" cy="18" rx="5" ry="7" />
                    <ellipse cx="57" cy="20" rx="5" ry="7" />
                    <ellipse cx="70" cy="30" rx="4.5" ry="6.5" />
                    <ellipse cx="47" cy="55" rx="14" ry="17" />
                  </g>

                  <!-- ПЯТНА -->
                  <g fill="#7b4a2c" opacity="0.9">
                    <circle cx="15" cy="18" r="2.2" />
                    <circle cx="28" cy="14" r="2.5" />
                    <circle cx="35" cy="28" r="2" />
                    <circle cx="48" cy="10" r="2.2" />
                    <circle cx="63" cy="14" r="2.4" />
                    <circle cx="76" cy="24" r="2" />
                    <circle cx="19" cy="42" r="2.5" />
                    <circle cx="31" cy="49" r="2" />
                    <circle cx="63" cy="43" r="2.3" />
                    <circle cx="72" cy="55" r="2" />
                    <circle cx="35" cy="69" r="2.3" />
                    <circle cx="58" cy="72" r="2.5" />
                  </g>

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
                <span class="paw-spark spark-two">✦</span>
                <span class="paw-spark spark-three">✧</span>
              </div>
            </div>
          </Transition>
        </div>

        <!-- РЕЗУЛЬТАТ -->
        <div v-if="showResult" class="text-center">
          <p
            v-if="isCorrect"
            class="text-green-600 font-semibold mb-2"
          >
            ✅ Верно!
          </p>

          <p
            v-else
            class="text-red-600 font-semibold mb-2 break-words leading-relaxed"
          >
            ❌ Неверно. Правильно:
            <b>{{ currentTask.correct }}</b>
          </p>

          <button
            @click="nextTask"
            class="mt-3 w-full bg-purple-600 hover:bg-purple-700 text-white font-bold py-3 px-4 rounded-xl transition"
          >
            Следующий
          </button>
        </div>

        <!-- КНОПКА ПРОВЕРИТЬ -->
        <button
          v-else
          @click="checkAnswer"
          class="w-full bg-yellow-500 hover:bg-yellow-600 text-white font-bold py-3 px-4 rounded-xl transition"
        >
          Проверить
        </button>

        <!-- НАЗАД / ВПЕРЁД -->
        <div class="flex justify-between gap-3 mt-6">
          <button
            @click="prevTask"
            :disabled="currentIdx === 0"
            class="flex-1 min-w-0 px-3 sm:px-4 py-3 bg-gray-300 hover:bg-gray-400 disabled:bg-gray-200 disabled:text-gray-400 rounded-xl font-semibold transition text-sm sm:text-base"
          >
            ⬅️ Назад
          </button>

          <button
            @click="nextUnlocked"
            :disabled="currentIdx === tasks.length - 1"
            class="flex-1 min-w-0 px-3 sm:px-4 py-3 bg-gray-300 hover:bg-gray-400 disabled:bg-gray-200 disabled:text-gray-400 rounded-xl font-semibold transition text-sm sm:text-base"
          >
            Вперед ➡️
          </button>
        </div>
      </div>
    </div>

    <!-- ОКНО НУМЕРАЦИИ -->
    <div
      v-if="!finished"
      class="mx-auto mt-4 w-full max-w-2xl grid grid-cols-5 gap-2 p-3 bg-white rounded-xl shadow-lg border border-yellow-200 md:absolute md:right-4 md:top-4 md:mt-0 md:w-72"
    >
      <div
        v-for="(t, index) in tasks"
        :key="index"
        @click="jumpTo(index)"
        class="w-full h-10 sm:h-11 flex items-center justify-center rounded-lg cursor-pointer border font-semibold text-sm sm:text-base transition"
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

    <!-- ФИНАЛ -->
    <div
      v-if="finished"
      class="mx-auto w-full max-w-2xl bg-white rounded-2xl shadow-2xl p-4 sm:p-8 border-t-4 border-yellow-400"
    >
      <div class="text-center mt-2 sm:mt-6">
        <div class="bg-white rounded-xl shadow-lg p-5 sm:p-6 inline-block w-full">
          <p class="text-2xl sm:text-3xl font-bold text-purple-700 mb-2">
            🎉 Ваши результаты
          </p>

          <p class="text-base sm:text-lg mb-2 leading-relaxed">
            Вы выполнили задание «Найди ошибку в ударении»
          </p>

          <p class="text-3xl sm:text-4xl font-extrabold text-green-600 mb-2">
            {{ correctCount }}/{{ tasks.length }}
          </p>

          <p class="text-2xl font-bold text-blue-600 mb-2">
            {{ percent }}%
          </p>

          <p class="text-xl font-bold mb-4">
            Оценка: {{ grade }}
          </p>

          <p
            v-if="saveStatus === 'saving'"
            class="text-gray-500 mb-4"
          >
            ⏳ Сохраняем результат...
          </p>

          <p
            v-else-if="saveStatus === 'saved'"
            class="text-green-600 font-semibold mb-4"
          >
            ✅ Результат сохранён
          </p>

          <p
            v-else-if="saveStatus === 'error'"
            class="text-red-600 font-semibold mb-4"
          >
            ⚠️ Не удалось сохранить результат
          </p>

          <div class="flex flex-col gap-3">
            <NuxtLink
              to="/results"
              class="block bg-purple-600 hover:bg-purple-700 text-white font-bold py-3 px-6 rounded-lg transition"
            >
              📊 Мой прогресс
            </NuxtLink>

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
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'

interface Task {
  row: string
  correct: string
}

interface Student {
  name: string
  class: string
}

const supabase = useSupabaseClient()

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

const studentName = ref('')
const studentClass = ref('')

const attemptId = ref<string | number | null>(null)
const startedAt = ref('')

const saveStatus = ref<'saving' | 'saved' | 'error'>('saving')

const finished = ref(false)

const showPaw = ref(false)
let pawTimer: ReturnType<typeof setTimeout> | null = null

const currentTask = computed(() => tasks[currentIdx.value]!)

const percent = computed(() =>
  Math.round((correctCount.value / tasks.length) * 100)
)

const grade = computed(() => {
  if (percent.value < 50) return 2
  if (percent.value <= 70) return 3
  if (percent.value <= 84) return 4
  return 5
})

function triggerCorrectPaw() {
  if (pawTimer) {
    clearTimeout(pawTimer)
  }

  showPaw.value = true

  pawTimer = setTimeout(() => {
    showPaw.value = false
    pawTimer = null

    if (currentIdx.value === tasks.length - 1) {
      finished.value = true
      saveResult()
    }
  }, 3000)
}

onMounted(async () => {
  const savedStudent = localStorage.getItem('orphoepia_student')

  if (!savedStudent) {
    await navigateTo('/')
    return
  }

  try {
    const student: Student = JSON.parse(savedStudent)

    if (!student.name?.trim() || !student.class?.trim()) {
      await navigateTo('/')
      return
    }

    studentName.value = student.name.trim()
    studentClass.value = student.class.trim()

    startedAt.value = new Date().toISOString()

    const { data, error } = await supabase
      .from('attempts')
      .insert({
        student_name: studentName.value,
        class: studentClass.value,
        task_name: 'Найди ошибку в ударении',
        total_questions: tasks.length,
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
    saveStatus.value = 'saving'
  } catch (error) {
    console.error('Ошибка чтения данных ученика:', error)
    await navigateTo('/')
  }
})

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

function checkAnswer() {
  if (!userAnswer.value.trim()) return
  if (showResult.value) return

  showResult.value = true

  isCorrect.value =
    userAnswer.value.trim().toLowerCase() ===
    currentTask.value.correct.toLowerCase()

  answered.value[currentIdx.value] = true

  if (isCorrect.value) {
    correctCount.value++
    triggerCorrectPaw()
  }
}

function nextTask() {
  showPaw.value = false

  if (pawTimer) {
    clearTimeout(pawTimer)
    pawTimer = null
  }

  if (currentIdx.value < tasks.length - 1) {
    currentIdx.value++
    userAnswer.value = ''
    showResult.value = false
    isCorrect.value = false
  } else {
    finished.value = true
    saveResult()
  }
}

function prevTask() {
  showPaw.value = false

  if (pawTimer) {
    clearTimeout(pawTimer)
    pawTimer = null
  }

  if (currentIdx.value > 0) {
    currentIdx.value--
    userAnswer.value = ''
    showResult.value = false
    isCorrect.value = false
  }
}

function nextUnlocked() {
  showPaw.value = false

  if (pawTimer) {
    clearTimeout(pawTimer)
    pawTimer = null
  }

  if (currentIdx.value < tasks.length - 1) {
    currentIdx.value++
    userAnswer.value = ''
    showResult.value = false
    isCorrect.value = false
  }
}

function jumpTo(index: number) {
  showPaw.value = false

  if (pawTimer) {
    clearTimeout(pawTimer)
    pawTimer = null
  }

  currentIdx.value = index
  userAnswer.value = ''
  showResult.value = false
  isCorrect.value = false
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

/* ЛАПКА */
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

/* ПОЯВЛЕНИЕ / ИСЧЕЗНОВЕНИЕ */
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

/* ЖИВОЕ ДВИЖЕНИЕ */
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
