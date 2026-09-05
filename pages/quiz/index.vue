<template>
  <div
    class="min-h-screen bg-gradient-to-br from-amber-100 via-pink-50 to-purple-100 px-4 py-6 sm:py-10"
  >
    <!-- ===================== ВИКТОРИНА ===================== -->
    <div v-if="!finished" class="mx-auto max-w-5xl">

      <!-- Верхняя часть -->
      <div
        class="relative overflow-hidden rounded-[30px] border border-amber-200 bg-white/95 p-5 shadow-2xl sm:p-8"
      >
        <!-- декоративные кружочки -->
        <div class="pointer-events-none absolute -right-16 -top-16 h-40 w-40 rounded-full bg-yellow-200/40"></div>
        <div class="pointer-events-none absolute -bottom-20 -left-16 h-44 w-44 rounded-full bg-pink-200/40"></div>

        <!-- Заголовок -->
        <div class="relative z-10 text-center">
          <div
            class="mb-3 inline-flex items-center rounded-full bg-amber-100 px-5 py-2 text-sm font-bold text-amber-800 shadow-sm"
          >
            ✍️ Орфоэпический тренажёр
          </div>

          <h1
            class="text-2xl font-black tracking-tight text-gray-800 sm:text-4xl"
          >
            ✍️ Найди ошибку в ударении
          </h1>

          <p class="mx-auto mt-3 max-w-2xl text-sm leading-6 text-gray-600 sm:text-base">
            В каждом ряду одно слово с неправильным ударением.
            <span class="font-bold text-amber-700">
              Введите его правильно.
            </span>
          </p>
        </div>

        <!-- Прогресс -->
        <div class="relative z-10 mt-7">
          <div class="mb-2 flex items-center justify-between text-xs font-bold text-gray-500">
            <span>Вопрос {{ currentIdx + 1 }} из {{ tasks.length }}</span>
            <span>{{ Math.round(((currentIdx + 1) / tasks.length) * 100) }}%</span>
          </div>

          <div class="h-3 overflow-hidden rounded-full bg-amber-100">
            <div
              class="h-full rounded-full bg-gradient-to-r from-yellow-400 via-orange-400 to-pink-400 transition-all duration-500"
              :style="{ width: `${((currentIdx + 1) / tasks.length) * 100}%` }"
            ></div>
          </div>
        </div>

        <!-- Задание -->
        <div
          class="relative z-10 mt-8 rounded-[26px] border-2 border-amber-200 bg-gradient-to-br from-[#fffaf0] to-[#fff4dc] p-5 shadow-inner sm:p-8"
        >
          <div class="mb-5 text-center">
            <span
              class="inline-flex items-center justify-center rounded-full bg-amber-200 px-4 py-1.5 text-sm font-black text-amber-900"
            >
              Задание №{{ currentIdx + 1 }}
            </span>
          </div>

          <!-- Строка слов -->
          <div
            class="rounded-2xl border border-amber-200 bg-white/80 px-4 py-5 text-center text-lg font-bold leading-9 text-gray-800 shadow-sm sm:px-8 sm:text-xl"
          >
            {{ currentTask.row }}
          </div>

          <!-- Поле ответа -->
          <div class="relative mx-auto mt-8 max-w-2xl">
            <label
              class="mb-2 block text-center text-sm font-bold text-gray-600"
            >
              Введите правильный вариант:
            </label>

            <div class="relative">
              <input
                v-model="userAnswer"
                :disabled="showResult"
                type="text"
                autocomplete="off"
                placeholder="Например: жалюзИ"
                class="answer-input w-full rounded-2xl border-3 px-5 py-4 pr-28 text-center text-lg font-black outline-none transition-all duration-300 placeholder:font-medium placeholder:text-gray-400"
                :class="{
                  'border-gray-300 bg-white shadow-md focus:border-amber-400 focus:ring-4 focus:ring-amber-100':
                    !showResult,

                  'border-green-500 bg-green-100 text-green-800 shadow-[0_0_25px_rgba(34,197,94,0.35)] ring-4 ring-green-200':
                    showResult && isCorrect,

                  'border-red-500 bg-red-100 text-red-800 shadow-[0_0_20px_rgba(239,68,68,0.25)] ring-4 ring-red-200':
                    showResult && !isCorrect
                }"
                @keyup.enter="checkAnswer"
              />

              <!-- Зеленая галочка -->
              <div
                v-if="showResult && isCorrect"
                class="absolute right-5 top-1/2 z-20 flex h-10 w-10 -translate-y-1/2 items-center justify-center rounded-full bg-green-500 text-2xl font-black text-white shadow-lg"
              >
                ✓
              </div>

              <!-- Красный крест -->
              <div
                v-if="showResult && !isCorrect"
                class="absolute right-5 top-1/2 z-20 flex h-10 w-10 -translate-y-1/2 items-center justify-center rounded-full bg-red-500 text-2xl font-black text-white shadow-lg"
              >
                ×
              </div>

              <!-- ================= ЛАПКА ================= -->
              <div
                v-if="showPaw && isCorrect"
                class="paw-wrapper pointer-events-none absolute -right-7 top-1/2 z-30 -translate-y-1/2 sm:-right-12"
              >
                <div class="paw-spark spark-one">✦</div>
                <div class="paw-spark spark-two">✦</div>
                <div class="paw-spark spark-three">✧</div>
                <div class="paw-spark spark-four">✦</div>

                <svg
                  class="paw-svg"
                  viewBox="0 0 120 120"
                  xmlns="http://www.w3.org/2000/svg"
                >
                  <defs>
                    <radialGradient
                      id="pawFur"
                      cx="35%"
                      cy="30%"
                      r="75%"
                    >
                      <stop offset="0%" stop-color="#f8d58a" />
                      <stop offset="45%" stop-color="#d99538" />
                      <stop offset="80%" stop-color="#a85d20" />
                      <stop offset="100%" stop-color="#743914" />
                    </radialGradient>

                    <radialGradient
                      id="pawPad"
                      cx="35%"
                      cy="25%"
                      r="75%"
                    >
                      <stop offset="0%" stop-color="#ffd5c4" />
                      <stop offset="55%" stop-color="#ee8c87" />
                      <stop offset="100%" stop-color="#bd4e58" />
                    </radialGradient>

                    <filter id="pawShadow">
                      <feDropShadow
                        dx="0"
                        dy="7"
                        stdDeviation="5"
                        flood-opacity="0.35"
                      />
                    </filter>
                  </defs>

                  <!-- лапка -->
                  <g filter="url(#pawShadow)">
                    <!-- пальцы -->
                    <ellipse
                      cx="28"
                      cy="35"
                      rx="14"
                      ry="20"
                      transform="rotate(-28 28 35)"
                      fill="url(#pawFur)"
                    />
                    <ellipse
                      cx="48"
                      cy="22"
                      rx="14"
                      ry="21"
                      transform="rotate(-10 48 22)"
                      fill="url(#pawFur)"
                    />
                    <ellipse
                      cx="69"
                      cy="22"
                      rx="14"
                      ry="21"
                      transform="rotate(12 69 22)"
                      fill="url(#pawFur)"
                    />
                    <ellipse
                      cx="90"
                      cy="35"
                      rx="14"
                      ry="20"
                      transform="rotate(28 90 35)"
                      fill="url(#pawFur)"
                    />

                    <!-- основа -->
                    <path
                      d="M27 58
                         C25 47 34 40 46 43
                         C52 45 56 48 60 48
                         C65 48 70 44 76 43
                         C88 40 97 48 95 60
                         C94 69 104 78 99 91
                         C94 105 78 109 61 108
                         C43 108 27 103 23 90
                         C19 77 29 69 27 58Z"
                      fill="url(#pawFur)"
                    />

                    <!-- леопардовые пятна -->
                    <g fill="#4f2917" opacity="0.9">
                      <ellipse cx="31" cy="30" rx="4" ry="3" transform="rotate(-25 31 30)" />
                      <ellipse cx="44" cy="14" rx="4" ry="3" transform="rotate(15 44 14)" />
                      <ellipse cx="55" cy="29" rx="4" ry="3" transform="rotate(-18 55 29)" />
                      <ellipse cx="74" cy="13" rx="4" ry="3" transform="rotate(-15 74 13)" />
                      <ellipse cx="84" cy="29" rx="4" ry="3" transform="rotate(22 84 29)" />

                      <ellipse cx="39" cy="58" rx="5" ry="3" transform="rotate(25 39 58)" />
                      <ellipse cx="51" cy="70" rx="4" ry="3" transform="rotate(-15 51 70)" />
                      <ellipse cx="77" cy="58" rx="5" ry="3" transform="rotate(-20 77 58)" />
                      <ellipse cx="87" cy="73" rx="4" ry="3" transform="rotate(20 87 73)" />
                      <ellipse cx="34" cy="82" rx="4" ry="3" transform="rotate(-20 34 82)" />
                      <ellipse cx="48" cy="94" rx="5" ry="3" transform="rotate(20 48 94)" />
                      <ellipse cx="72" cy="91" rx="5" ry="3" transform="rotate(-25 72 91)" />
                      <ellipse cx="91" cy="88" rx="4" ry="3" transform="rotate(15 91 88)" />
                    </g>

                    <!-- пятна-кольца -->
                    <g fill="none" stroke="#4f2917" stroke-width="3">
                      <ellipse cx="27" cy="70" rx="5" ry="7" />
                      <ellipse cx="62" cy="63" rx="6" ry="5" />
                      <ellipse cx="81" cy="83" rx="6" ry="7" />
                      <ellipse cx="59" cy="94" rx="6" ry="4" />
                    </g>

                    <!-- подушечки пальцев -->
                    <ellipse cx="28" cy="36" rx="7" ry="9" fill="url(#pawPad)" />
                    <ellipse cx="48" cy="22" rx="7" ry="10" fill="url(#pawPad)" />
                    <ellipse cx="69" cy="22" rx="7" ry="10" fill="url(#pawPad)" />
                    <ellipse cx="90" cy="36" rx="7" ry="9" fill="url(#pawPad)" />

                    <!-- центральная подушечка -->
                    <path
                      d="M42 68
                         C40 58 49 52 60 55
                         C71 52 80 58 78 68
                         C77 76 70 82 60 83
                         C50 82 43 76 42 68Z"
                      fill="url(#pawPad)"
                    />

                    <!-- блики -->
                    <ellipse
                      cx="51"
                      cy="64"
                      rx="5"
                      ry="2.5"
                      fill="#fff"
                      opacity="0.5"
                      transform="rotate(-25 51 64)"
                    />

                    <ellipse
                      cx="45"
                      cy="19"
                      rx="2.5"
                      ry="4"
                      fill="#fff"
                      opacity="0.45"
                    />
                  </g>
                </svg>
              </div>
            </div>

            <!-- кнопка -->
            <button
              v-if="!showResult"
              @click="checkAnswer"
              class="mt-5 w-full rounded-2xl bg-gradient-to-r from-amber-400 via-orange-400 to-pink-400 px-6 py-4 text-base font-black text-white shadow-lg transition-all duration-200 hover:-translate-y-1 hover:shadow-xl active:translate-y-0"
            >
              Проверить ответ ✓
            </button>
          </div>

          <!-- Результат -->
          <transition name="result">
            <div
              v-if="showResult"
              class="mx-auto mt-7 max-w-2xl rounded-2xl border-2 p-5 text-center"
              :class="
                isCorrect
                  ? 'border-green-300 bg-green-100'
                  : 'border-red-300 bg-red-100'
              "
            >
              <div
                v-if="isCorrect"
                class="text-xl font-black text-green-700"
              >
                🎉 Верно!
              </div>

              <div
                v-else
                class="text-xl font-black text-red-700"
              >
                ❌ Неверно
              </div>

              <p
                v-if="!isCorrect"
                class="mt-2 text-sm font-semibold text-red-700"
              >
                Правильный вариант:
                <span class="font-black">
                  {{ currentTask.correct }}
                </span>
              </p>

              <p
                v-else
                class="mt-2 text-sm font-semibold text-green-700"
              >
                Отлично! Ударение указано правильно.
              </p>
            </div>
          </transition>
        </div>

        <!-- Навигация -->
        <div class="relative z-10 mt-7 flex items-center justify-between gap-3">
          <button
            @click="prevTask"
            :disabled="currentIdx === 0"
            class="rounded-xl border-2 border-gray-200 bg-white px-4 py-3 text-sm font-bold text-gray-600 shadow-sm transition hover:bg-gray-50 disabled:cursor-not-allowed disabled:opacity-40 sm:px-6"
          >
            ← Назад
          </button>

          <button
            v-if="currentIdx < tasks.length - 1"
            @click="nextUnlocked"
            class="rounded-xl bg-gray-800 px-5 py-3 text-sm font-bold text-white shadow-md transition hover:bg-gray-700 sm:px-7"
          >
            Далее →
          </button>

          <button
            v-else
            @click="nextTask"
            class="rounded-xl bg-gradient-to-r from-green-500 to-emerald-600 px-5 py-3 text-sm font-black text-white shadow-md transition hover:-translate-y-0.5 sm:px-7"
          >
            Завершить ✓
          </button>
        </div>
      </div>

      <!-- ================= НУМЕРАЦИЯ ================= -->
      <div
        class="mt-6 rounded-[26px] border-2 border-[#d7b98b] bg-[#f7ead2] p-4 shadow-xl sm:p-6"
      >
        <div class="mb-4 text-center">
          <span class="text-sm font-black text-[#795548]">
            📋 Вопросы
          </span>
        </div>

        <div class="grid grid-cols-5 gap-2.5 sm:gap-3">
          <button
            v-for="(_, index) in tasks"
            :key="index"
            @click="jumpTo(index)"
            class="number-button flex aspect-square items-center justify-center rounded-xl text-sm font-black shadow-sm transition-all duration-200 sm:text-base"
            :class="{
              'scale-105 bg-blue-500 text-white shadow-lg ring-4 ring-blue-200':
                currentIdx === index,

              'bg-green-500 text-white shadow-md hover:bg-green-600':
                currentIdx !== index && answered[index],

              'bg-[#fffaf1] text-[#795548] hover:-translate-y-0.5 hover:bg-white':
                currentIdx !== index && !answered[index]
            }"
          >
            {{ index + 1 }}
          </button>
        </div>

        <div class="mt-5 flex flex-wrap justify-center gap-4 text-xs font-semibold text-gray-600">
          <div class="flex items-center gap-1.5">
            <span class="h-3 w-3 rounded bg-blue-500"></span>
            Текущий
          </div>

          <div class="flex items-center gap-1.5">
            <span class="h-3 w-3 rounded bg-green-500"></span>
            Отвечено
          </div>

          <div class="flex items-center gap-1.5">
            <span class="h-3 w-3 rounded bg-[#fffaf1] border border-[#d7b98b]"></span>
            Не отвечено
          </div>
        </div>
      </div>
    </div>

    <!-- ===================== РЕЗУЛЬТАТ ===================== -->
    <div
      v-else
      class="mx-auto flex min-h-[80vh] max-w-2xl items-center justify-center"
    >
      <div
        class="w-full rounded-[32px] border-2 border-amber-200 bg-white p-7 text-center shadow-2xl sm:p-10"
      >
        <div class="text-6xl">🎉</div>

        <h2 class="mt-4 text-3xl font-black text-gray-800 sm:text-4xl">
          Тренажёр завершён!
        </h2>

        <p class="mt-3 text-gray-600">
          {{ studentName }}, вот твой результат:
        </p>

        <div
          class="mx-auto mt-7 max-w-sm rounded-3xl bg-gradient-to-br from-amber-100 to-pink-100 p-7"
        >
          <div class="text-sm font-bold text-gray-600">
            Правильных ответов
          </div>

          <div class="mt-1 text-5xl font-black text-gray-800">
            {{ correctCount }}/{{ tasks.length }}
          </div>

          <div class="mt-2 text-2xl font-black text-amber-600">
            {{ percent }}%
          </div>

          <div class="mt-3 text-lg font-black text-gray-700">
            Оценка: {{ grade }}
          </div>
        </div>

        <div
          v-if="saveStatus === 'saving'"
          class="mt-5 text-sm font-semibold text-gray-500"
        >
          ⏳ Сохраняем результат...
        </div>

        <div
          v-else-if="saveStatus === 'saved'"
          class="mt-5 text-sm font-bold text-green-600"
        >
          ✓ Результат сохранён
        </div>

        <div
          v-else
          class="mt-5 text-sm font-bold text-red-600"
        >
          ⚠️ Не удалось сохранить результат
        </div>

        <div class="mt-7 flex flex-col gap-3 sm:flex-row">
          <button
            @click="navigateTo('/results')"
            class="flex-1 rounded-2xl bg-gradient-to-r from-amber-400 to-orange-500 px-5 py-4 font-black text-white shadow-lg transition hover:-translate-y-0.5"
          >
            📊 Мои результаты
          </button>

          <button
            @click="navigateTo('/')"
            class="flex-1 rounded-2xl bg-gray-800 px-5 py-4 font-black text-white shadow-lg transition hover:bg-gray-700"
          >
            🏠 На главную
          </button>
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
.answer-input {
  border-width: 3px;
}

/* ================= ЛАПКА ================= */

.paw-wrapper {
  width: 105px;
  height: 105px;
  animation: pawAttack 1.25s cubic-bezier(0.22, 1, 0.36, 1) infinite;
}

.paw-svg {
  width: 105px;
  height: 105px;
  overflow: visible;
  filter:
    drop-shadow(0 8px 8px rgba(93, 45, 12, 0.35))
    drop-shadow(0 0 14px rgba(255, 183, 77, 0.65));
}

/* лапка будто оттягивает поле */
@keyframes pawAttack {
  0% {
    transform: translateX(55px) translateY(-50%) rotate(18deg) scale(0.78);
    opacity: 0;
  }

  18% {
    opacity: 1;
    transform: translateX(22px) translateY(-50%) rotate(10deg) scale(1.08);
  }

  38% {
    transform: translateX(-8px) translateY(-50%) rotate(-8deg) scale(1.18);
  }

  52% {
    transform: translateX(4px) translateY(-50%) rotate(-2deg) scale(1.1);
  }

  70% {
    transform: translateX(-3px) translateY(-50%) rotate(-6deg) scale(1.13);
  }

  100% {
    transform: translateX(45px) translateY(-50%) rotate(16deg) scale(0.9);
    opacity: 0;
  }
}

/* сияние */
.paw-wrapper::after {
  content: "";
  position: absolute;
  left: 5px;
  top: 12px;
  width: 90px;
  height: 90px;
  border-radius: 50%;
  background: radial-gradient(
    circle,
    rgba(255, 210, 90, 0.45),
    rgba(255, 210, 90, 0) 70%
  );
  z-index: -1;
  animation: pawGlow 0.8s ease-in-out infinite alternate;
}

@keyframes pawGlow {
  from {
    transform: scale(0.8);
    opacity: 0.45;
  }

  to {
    transform: scale(1.35);
    opacity: 0.9;
  }
}

/* искры */
.paw-spark {
  position: absolute;
  z-index: 40;
  color: #f59e0b;
  font-size: 24px;
  font-weight: 900;
  text-shadow:
    0 0 5px white,
    0 0 12px #fbbf24,
    0 0 20px #f59e0b;
  animation: sparkJump 0.75s ease-in-out infinite;
}

.spark-one {
  left: 2px;
  top: 2px;
}

.spark-two {
  right: -5px;
  top: 18px;
  animation-delay: 0.18s;
}

.spark-three {
  left: 18px;
  bottom: 2px;
  animation-delay: 0.35s;
}

.spark-four {
  right: 8px;
  bottom: 14px;
  animation-delay: 0.5s;
}

@keyframes sparkJump {
  0%,
  100% {
    transform: scale(0.6) rotate(0deg);
    opacity: 0.2;
  }

  50% {
    transform: scale(1.35) rotate(25deg);
    opacity: 1;
  }
}

/* результат */
.result-enter-active,
.result-leave-active {
  transition: all 0.35s ease;
}

.result-enter-from,
.result-leave-to {
  opacity: 0;
  transform: translateY(10px) scale(0.97);
}

/* номера */
.number-button {
  min-height: 45px;
}
</style>
