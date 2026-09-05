<template>
  <div
    class="relative min-h-screen bg-gradient-to-br from-amber-100 via-pink-50 to-purple-100 px-4 py-6 sm:px-6 lg:px-8"
  >
    <!-- ========================================================= -->
    <!-- ОСНОВНАЯ ОБЛАСТЬ -->
    <!-- ========================================================= -->

    <div
      v-if="!finished"
      class="relative mx-auto max-w-[1400px]"
    >
      <!-- ======================================================= -->
      <!-- ЗАДАНИЕ -->
      <!-- ======================================================= -->

      <div class="mx-auto max-w-5xl">
        <!-- Заголовок -->
        <div
          class="mb-5 rounded-[28px] border-2 border-purple-200 bg-white/95 p-5 shadow-xl sm:p-7"
        >
          <div class="mb-2 text-center text-3xl sm:text-4xl">
            ✍️
          </div>

          <h1
            class="text-center text-2xl font-extrabold text-purple-800 sm:text-3xl"
          >
            Найди ошибку в ударении
          </h1>

          <p
            class="mx-auto mt-3 max-w-2xl text-center text-sm leading-6 text-gray-600 sm:text-base"
          >
            В каждой строке одно слово имеет неправильное ударение.
            Напиши правильный вариант этого слова.
          </p>
        </div>

        <!-- Прогресс -->
        <div
          class="mb-5 rounded-2xl border-2 border-white/70 bg-white/80 p-4 shadow-lg"
        >
          <div class="mb-2 flex items-center justify-between">
            <span class="text-sm font-bold text-purple-700">
              Вопрос {{ currentIdx + 1 }} из {{ tasks.length }}
            </span>

            <span class="text-sm font-bold text-gray-500">
              {{ Math.round(((currentIdx + 1) / tasks.length) * 100) }}%
            </span>
          </div>

          <div class="h-3 overflow-hidden rounded-full bg-purple-100">
            <div
              class="h-full rounded-full bg-gradient-to-r from-purple-500 to-pink-500 transition-all duration-500"
              :style="{
                width: `${((currentIdx + 1) / tasks.length) * 100}%`
              }"
            />
          </div>
        </div>

        <!-- ===================================================== -->
        <!-- КАРТОЧКА ЗАДАНИЯ -->
        <!-- ===================================================== -->

        <div
          class="relative overflow-hidden rounded-[30px] border-2 border-amber-200 bg-white/95 p-5 shadow-2xl sm:p-8"
        >
          <!-- Декоративные элементы -->
          <div
            class="pointer-events-none absolute -right-16 -top-16 h-40 w-40 rounded-full bg-pink-100/60 blur-2xl"
          />

          <div
            class="pointer-events-none absolute -bottom-16 -left-16 h-40 w-40 rounded-full bg-purple-100/60 blur-2xl"
          />

          <!-- Номер -->
          <div
            class="relative z-10 mb-6 flex justify-center"
          >
            <div
              class="rounded-full bg-gradient-to-r from-purple-500 to-pink-500 px-5 py-2 text-sm font-extrabold text-white shadow-lg"
            >
              Вопрос {{ currentIdx + 1 }}
            </div>
          </div>

          <!-- Инструкция -->
          <div
            class="relative z-10 mb-5 rounded-2xl bg-amber-50 p-4 text-center"
          >
            <p class="font-semibold text-gray-700">
              Найди слово с ошибкой и напиши его правильно.
            </p>
          </div>

          <!-- Строка слов -->
          <div
            class="relative z-10 mb-8 rounded-[24px] border-2 border-purple-100 bg-gradient-to-br from-purple-50 to-pink-50 px-4 py-7 text-center shadow-inner sm:px-8"
          >
            <p
              class="break-words text-xl font-bold leading-loose text-gray-800 sm:text-2xl"
            >
              {{ currentTask.row }}
            </p>
          </div>

          <!-- Поле ответа + лапка -->
          <div
            class="relative z-10 mx-auto max-w-2xl"
          >
            <label
              class="mb-3 block text-center text-base font-extrabold text-purple-700 sm:text-lg"
            >
              ✏️ Твой ответ
            </label>

            <div
              class="relative flex items-center gap-2"
            >
              <!-- Поле -->
              <input
                v-model="userAnswer"
                type="text"
                autocomplete="off"
                :disabled="showResult"
                placeholder="Напиши правильное ударение..."
                class="h-14 min-w-0 flex-1 rounded-2xl border-3 border-purple-200 bg-white px-5 text-center text-lg font-bold text-gray-800 shadow-lg outline-none transition-all placeholder:text-sm placeholder:font-medium placeholder:text-gray-400 focus:border-purple-500 focus:ring-4 focus:ring-purple-100 disabled:bg-gray-50 sm:text-xl"
                @keyup.enter="checkAnswer"
              />

              <!-- ЛАПКА -->
              <transition name="paw-attack">
                <div
                  v-if="showPaw"
                  class="paw-wrapper pointer-events-none absolute -right-4 top-1/2 z-30 -translate-y-1/2 sm:-right-8"
                >
                  <svg
                    class="paw-svg"
                    viewBox="0 0 120 120"
                    xmlns="http://www.w3.org/2000/svg"
                  >
                    <defs>
                      <radialGradient
                        id="pawFur"
                        cx="35%"
                        cy="25%"
                        r="80%"
                      >
                        <stop
                          offset="0%"
                          stop-color="#f6c66a"
                        />
                        <stop
                          offset="45%"
                          stop-color="#d89a3d"
                        />
                        <stop
                          offset="100%"
                          stop-color="#9a5d20"
                        />
                      </radialGradient>

                      <radialGradient
                        id="pawPad"
                        cx="35%"
                        cy="25%"
                        r="80%"
                      >
                        <stop
                          offset="0%"
                          stop-color="#ffb6c9"
                        />
                        <stop
                          offset="100%"
                          stop-color="#e85d7f"
                        />
                      </radialGradient>

                      <filter
                        id="pawShadow"
                        x="-50%"
                        y="-50%"
                        width="200%"
                        height="200%"
                      >
                        <feDropShadow
                          dx="0"
                          dy="5"
                          stdDeviation="4"
                          flood-opacity=".35"
                        />
                      </filter>
                    </defs>

                    <!-- Внешнее свечение -->
                    <circle
                      cx="60"
                      cy="60"
                      r="48"
                      fill="rgba(255,210,80,.22)"
                    />

                    <!-- Большая подушечка -->
                    <ellipse
                      cx="60"
                      cy="72"
                      rx="31"
                      ry="27"
                      fill="url(#pawFur)"
                      stroke="#713c17"
                      stroke-width="2"
                      filter="url(#pawShadow)"
                    />

                    <!-- Леопардовые пятна -->
                    <ellipse
                      cx="44"
                      cy="65"
                      rx="5"
                      ry="7"
                      fill="#4a2813"
                      transform="rotate(-25 44 65)"
                    />
                    <ellipse
                      cx="76"
                      cy="62"
                      rx="5"
                      ry="8"
                      fill="#4a2813"
                      transform="rotate(25 76 62)"
                    />
                    <ellipse
                      cx="52"
                      cy="86"
                      rx="4"
                      ry="5"
                      fill="#4a2813"
                      transform="rotate(20 52 86)"
                    />
                    <ellipse
                      cx="70"
                      cy="87"
                      rx="4"
                      ry="5"
                      fill="#4a2813"
                      transform="rotate(-20 70 87)"
                    />

                    <!-- Подушечка -->
                    <ellipse
                      cx="60"
                      cy="72"
                      rx="17"
                      ry="15"
                      fill="url(#pawPad)"
                    />

                    <!-- Пальчики -->
                    <ellipse
                      cx="28"
                      cy="45"
                      rx="11"
                      ry="16"
                      fill="url(#pawFur)"
                      stroke="#713c17"
                      stroke-width="2"
                      transform="rotate(-35 28 45)"
                    />

                    <ellipse
                      cx="47"
                      cy="31"
                      rx="11"
                      ry="17"
                      fill="url(#pawFur)"
                      stroke="#713c17"
                      stroke-width="2"
                      transform="rotate(-12 47 31)"
                    />

                    <ellipse
                      cx="69"
                      cy="31"
                      rx="11"
                      ry="17"
                      fill="url(#pawFur)"
                      stroke="#713c17"
                      stroke-width="2"
                      transform="rotate(12 69 31)"
                    />

                    <ellipse
                      cx="91"
                      cy="45"
                      rx="11"
                      ry="16"
                      fill="url(#pawFur)"
                      stroke="#713c17"
                      stroke-width="2"
                      transform="rotate(35 91 45)"
                    />

                    <!-- Розовые подушечки пальцев -->
                    <ellipse
                      cx="28"
                      cy="47"
                      rx="6"
                      ry="8"
                      fill="#ed7190"
                    />

                    <ellipse
                      cx="47"
                      cy="33"
                      rx="6"
                      ry="8"
                      fill="#ed7190"
                    />

                    <ellipse
                      cx="69"
                      cy="33"
                      rx="6"
                      ry="8"
                      fill="#ed7190"
                    />

                    <ellipse
                      cx="91"
                      cy="47"
                      rx="6"
                      ry="8"
                      fill="#ed7190"
                    />

                    <!-- Блики -->
                    <ellipse
                      cx="52"
                      cy="65"
                      rx="5"
                      ry="3"
                      fill="rgba(255,255,255,.45)"
                    />

                    <!-- Искры -->
                    <g class="spark spark-1">
                      <path
                        d="M15 18 L18 27 L27 30 L18 33 L15 42 L12 33 L3 30 L12 27 Z"
                        fill="#ffd43b"
                      />
                    </g>

                    <g class="spark spark-2">
                      <path
                        d="M105 13 L108 21 L116 24 L108 27 L105 35 L102 27 L94 24 L102 21 Z"
                        fill="#ffcc33"
                      />
                    </g>

                    <g class="spark spark-3">
                      <path
                        d="M102 79 L104 85 L111 87 L104 89 L102 96 L100 89 L93 87 L100 85 Z"
                        fill="#ffdf55"
                      />
                    </g>
                  </svg>
                </div>
              </transition>
            </div>

            <!-- Кнопка -->
            <button
              v-if="!showResult"
              type="button"
              class="mt-5 w-full rounded-2xl bg-gradient-to-r from-purple-600 to-pink-500 px-6 py-4 text-lg font-extrabold text-white shadow-xl transition-all duration-200 hover:-translate-y-0.5 hover:shadow-2xl active:translate-y-0"
              @click="checkAnswer"
            >
              Проверить ответ ✓
            </button>
          </div>

          <!-- =================================================== -->
          <!-- РЕЗУЛЬТАТ -->
          <!-- =================================================== -->

          <transition name="result">
            <div
              v-if="showResult"
              class="relative z-10 mx-auto mt-7 max-w-2xl"
            >
              <!-- Правильно -->
              <div
                v-if="isCorrect"
                class="rounded-[24px] border-4 border-green-400 bg-green-100 p-5 text-center shadow-xl sm:p-6"
              >
                <div class="mb-2 text-4xl">
                  🎉
                </div>

                <p
                  class="text-xl font-extrabold text-green-700 sm:text-2xl"
                >
                  Правильно!
                </p>

                <p
                  class="mt-2 font-semibold text-green-700"
                >
                  Отлично! Ты правильно нашла ошибку.
                </p>
              </div>

              <!-- Неправильно -->
              <div
                v-else
                class="rounded-[24px] border-4 border-red-300 bg-red-50 p-5 text-center shadow-xl sm:p-6"
              >
                <div class="mb-2 text-4xl">
                  💡
                </div>

                <p
                  class="text-xl font-extrabold text-red-600 sm:text-2xl"
                >
                  Попробуй ещё раз!
                </p>

                <p
                  class="mt-2 text-gray-700"
                >
                  Правильный вариант:
                  <span
                    class="font-extrabold text-green-600"
                  >
                    {{ currentTask.correct }}
                  </span>
                </p>
              </div>
            </div>
          </transition>

          <!-- =================================================== -->
          <!-- КНОПКИ ПЕРЕХОДА -->
          <!-- =================================================== -->

          <div
            class="relative z-10 mt-8 flex flex-col gap-3 sm:flex-row sm:justify-between"
          >
            <button
              type="button"
              :disabled="currentIdx === 0"
              class="rounded-2xl border-2 border-purple-200 bg-white px-6 py-3 font-extrabold text-purple-700 shadow-md transition-all hover:bg-purple-50 disabled:cursor-not-allowed disabled:opacity-40"
              @click="prevTask"
            >
              ← Назад
            </button>

            <button
              v-if="showResult && currentIdx < tasks.length - 1"
              type="button"
              class="rounded-2xl bg-gradient-to-r from-purple-600 to-pink-500 px-7 py-3 font-extrabold text-white shadow-lg transition-all hover:-translate-y-0.5 hover:shadow-xl"
              @click="nextUnlocked"
            >
              Следующий вопрос →
            </button>

            <button
              v-if="showResult && currentIdx === tasks.length - 1"
              type="button"
              class="rounded-2xl bg-gradient-to-r from-green-500 to-emerald-500 px-7 py-3 font-extrabold text-white shadow-lg transition-all hover:-translate-y-0.5 hover:shadow-xl"
              @click="nextTask"
            >
              Завершить ✓
            </button>
          </div>
        </div>
      </div>

      <!-- ======================================================= -->
      <!-- ОКНО НУМЕРАЦИИ -->
      <!-- ======================================================= -->

      <div
        class="
          mt-6
          rounded-[26px]
          border-2 border-[#d7b98b]
          bg-[#f7ead2]
          p-4
          shadow-xl
          sm:p-5

          lg:absolute
          lg:right-0
          lg:top-0
          lg:mt-0
          lg:w-72
        "
      >
        <div
          class="mb-4 text-center text-base font-extrabold text-[#795548]"
        >
          Номера вопросов
        </div>

        <div
          class="grid grid-cols-5 gap-2"
        >
          <button
            v-for="(item, index) in tasks"
            :key="index"
            type="button"
            :class="[
              'min-h-[45px] rounded-xl border-2 text-sm font-extrabold transition-all duration-200',

              currentIdx === index
                ? 'border-purple-600 bg-purple-500 text-white shadow-lg scale-105'
                : answered[index]
                  ? 'border-green-500 bg-green-400 text-white shadow-md'
                  : 'border-[#d7b98b] bg-[#fff8eb] text-[#795548] hover:bg-white hover:scale-105'
            ]"
            @click="jumpTo(index)"
          >
            {{ index + 1 }}
          </button>
        </div>

        <!-- Легенда -->
        <div
          class="mt-4 space-y-2 text-xs font-semibold text-[#795548]"
        >
          <div class="flex items-center gap-2">
            <span
              class="h-4 w-4 rounded-md border-2 border-purple-600 bg-purple-500"
            />
            Текущий вопрос
          </div>

          <div class="flex items-center gap-2">
            <span
              class="h-4 w-4 rounded-md border-2 border-green-500 bg-green-400"
            />
            Отвечено
          </div>

          <div class="flex items-center gap-2">
            <span
              class="h-4 w-4 rounded-md border-2 border-[#d7b98b] bg-[#fff8eb]"
            />
            Не отвечено
          </div>
        </div>
      </div>
    </div>

    <!-- ========================================================= -->
    <!-- ФИНАЛЬНЫЙ РЕЗУЛЬТАТ -->
    <!-- ========================================================= -->

    <div
      v-else
      class="mx-auto flex min-h-[80vh] max-w-3xl items-center justify-center"
    >
      <div
        class="w-full rounded-[32px] border-2 border-purple-200 bg-white p-7 text-center shadow-2xl sm:p-10"
      >
        <div class="mb-4 text-6xl">
          🐆
        </div>

        <h2
          class="text-3xl font-extrabold text-purple-800 sm:text-4xl"
        >
          Задание завершено!
        </h2>

        <p
          class="mt-4 text-lg text-gray-600"
        >
          {{ studentName }}, твой результат:
        </p>

        <div
          class="mx-auto mt-7 max-w-sm rounded-[26px] bg-gradient-to-br from-purple-50 to-pink-50 p-7"
        >
          <div
            class="text-5xl font-extrabold text-purple-700"
          >
            {{ correctCount }} / {{ tasks.length }}
          </div>

          <div
            class="mt-2 text-xl font-bold text-gray-700"
          >
            {{ percent }}%
          </div>

          <div
            class="mt-4 text-4xl"
          >
            {{ grade === 5 ? '🏆' : grade === 4 ? '🌟' : grade === 3 ? '👍' : '💪' }}
          </div>

          <div
            class="mt-2 text-xl font-extrabold text-purple-700"
          >
            Оценка: {{ grade }}
          </div>
        </div>

        <div
          class="mt-6 text-sm"
          :class="{
            'text-green-600': saveStatus === 'saved',
            'text-orange-500': saveStatus === 'saving',
            'text-red-500': saveStatus === 'error'
          }"
        >
          <span v-if="saveStatus === 'saved'">
            ✓ Результат сохранён
          </span>

          <span v-else-if="saveStatus === 'saving'">
            Сохраняем результат...
          </span>

          <span v-else>
            Не удалось сохранить результат
          </span>
        </div>

        <button
          type="button"
          class="mt-7 rounded-2xl bg-gradient-to-r from-purple-600 to-pink-500 px-8 py-4 font-extrabold text-white shadow-xl transition-all hover:-translate-y-0.5 hover:shadow-2xl"
          @click="navigateTo('/')"
        >
          Вернуться к заданиям
        </button>
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
  {
    row: 'жАлюзи, языковОй (факт), позвонИшь, куУхонный',
    correct: 'жалюзИ'
  },
  {
    row: 'тОрты, красивЕе, позвонИшь, крапИва',
    correct: 'красИвее'
  },
  {
    row: 'договОр, пОняла, столЯр, алфавИт',
    correct: 'понялА'
  },
  {
    row: 'бАловать, вЕрба, позвонИшь, комбАйнер',
    correct: 'баловАть'
  },
  {
    row: 'завИдно, жалюзИ, каталОг, дремотА',
    correct: 'дремОта'
  },
  {
    row: 'приговОр, разОгнутый, газопровОд, рАкушка',
    correct: 'ракУшка'
  },
  {
    row: 'бантЫ, взялА, экспЕрт, чЕрпать',
    correct: 'бАнты'
  },
  {
    row: 'кружевА, агрономИя, зАговор, столЯр',
    correct: 'агронОмия'
  },
  {
    row: 'хозяевА, каучУк, ободрИть, житиЕ',
    correct: 'хозЯева'
  },
  {
    row: 'ветеринАрия, фарфОр, упрочЕние, взялА',
    correct: 'упрОчение'
  },
  {
    row: 'шАрфы, бытиЕ, подОгнутый, индУстрия',
    correct: 'индустрИя'
  },
  {
    row: 'досУг, боЯзнь, кладбИще, эксперт',
    correct: 'клАдбище'
  },
  {
    row: 'гУсеничный, трубопрОвод, врученА, киломЕтр',
    correct: 'трубопровОд'
  },
  {
    row: 'сливОвый, манЯщий, столЯр, диалОг',
    correct: 'слИвовый'
  },
  {
    row: 'рАзвитее, дефИс, Искра, тотчАс',
    correct: 'тОтчас'
  }
]

const currentIdx = ref(0)
const userAnswer = ref('')
const showResult = ref(false)
const isCorrect = ref(false)
const correctCount = ref(0)

const answered = ref<boolean[]>(
  Array(tasks.length).fill(false)
)

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
  Math.round(
    (correctCount.value / tasks.length) * 100
  )
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
  const savedStudent =
    localStorage.getItem('orphoepia_student')

  if (!savedStudent) {
    await navigateTo('/')
    return
  }

  try {
    const student: Student =
      JSON.parse(savedStudent)

    if (
      !student.name?.trim() ||
      !student.class?.trim()
    ) {
      await navigateTo('/')
      return
    }

    studentName.value =
      student.name.trim()

    studentClass.value =
      student.class.trim()

    startedAt.value =
      new Date().toISOString()

    const { data, error } =
      await supabase
        .from('attempts')
        .insert({
          student_name: studentName.value,
          class: studentClass.value,
          task_name:
            'Найди ошибку в ударении',
          total_questions: tasks.length,
          started_at: startedAt.value
        })
        .select('id')
        .single()

    if (error) {
      console.error(
        'Ошибка создания попытки:',
        error
      )

      saveStatus.value = 'error'
      return
    }

    attemptId.value = data.id
    saveStatus.value = 'saving'
  } catch (error) {
    console.error(
      'Ошибка чтения данных ученика:',
      error
    )

    await navigateTo('/')
  }
})

async function saveResult() {
  if (!attemptId.value) {
    saveStatus.value = 'error'
    return
  }

  saveStatus.value = 'saving'

  const completedAt =
    new Date().toISOString()

  const { error } =
    await supabase
      .from('attempts')
      .update({
        score: correctCount.value,
        percentage: percent.value,
        completed_at: completedAt
      })
      .eq('id', attemptId.value)

  if (error) {
    console.error(
      'Ошибка сохранения результата:',
      error
    )

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
    userAnswer.value
      .trim()
      .toLowerCase() ===
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

  if (
    currentIdx.value <
    tasks.length - 1
  ) {
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

  if (
    currentIdx.value <
    tasks.length - 1
  ) {
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
/* ========================================================= */
/* ЛАПКА */
/* ========================================================= */

.paw-wrapper {
  width: 105px;
  height: 105px;
  transform-origin: center;
}

.paw-svg {
  width: 105px;
  height: 105px;
  overflow: visible;
  animation:
    pawAttack 0.75s cubic-bezier(0.2, 1.4, 0.35, 1)
      infinite alternate,
    pawShake 0.28s ease-in-out infinite alternate;
  filter:
    drop-shadow(0 8px 8px rgba(75, 35, 10, 0.28))
    drop-shadow(0 0 10px rgba(255, 190, 50, 0.7));
}

@keyframes pawAttack {
  0% {
    transform:
      translateX(45px)
      translateY(4px)
      rotate(14deg)
      scale(0.82);
  }

  55% {
    transform:
      translateX(10px)
      translateY(-2px)
      rotate(-4deg)
      scale(1.02);
  }

  100% {
    transform:
      translateX(-8px)
      translateY(0)
      rotate(-12deg)
      scale(1.08);
  }
}

@keyframes pawShake {
  0% {
    filter:
      drop-shadow(0 8px 8px rgba(75, 35, 10, 0.25))
      drop-shadow(0 0 8px rgba(255, 190, 50, 0.55));
  }

  100% {
    filter:
      drop-shadow(0 10px 10px rgba(75, 35, 10, 0.32))
      drop-shadow(0 0 18px rgba(255, 210, 60, 0.95));
  }
}

/* ========================================================= */
/* ИСКРЫ */
/* ========================================================= */

.spark {
  transform-origin: center;
  animation: sparkJump 0.55s ease-in-out infinite alternate;
}

.spark-2 {
  animation-delay: 0.15s;
}

.spark-3 {
  animation-delay: 0.3s;
}

@keyframes sparkJump {
  0% {
    opacity: 0.45;
    transform: scale(0.65) rotate(0deg);
  }

  100% {
    opacity: 1;
    transform: scale(1.25) rotate(18deg);
  }
}

/* ========================================================= */
/* ПОЯВЛЕНИЕ ЛАПКИ */
/* ========================================================= */

.paw-attack-enter-active {
  animation: pawEnter 0.55s cubic-bezier(0.15, 1.4, 0.3, 1);
}

.paw-attack-leave-active {
  transition: all 0.2s ease;
}

.paw-attack-enter-from,
.paw-attack-leave-to {
  opacity: 0;
  transform:
    translate(90px, -50%)
    scale(0.5)
    rotate(25deg);
}

@keyframes pawEnter {
  0% {
    opacity: 0;
    transform:
      translate(100px, -50%)
      rotate(25deg)
      scale(0.45);
  }

  55% {
    opacity: 1;
    transform:
      translate(-18px, -50%)
      rotate(-10deg)
      scale(1.12);
  }

  75% {
    transform:
      translate(8px, -50%)
      rotate(8deg)
      scale(0.96);
  }

  100% {
    transform:
      translate(0, -50%)
      rotate(0deg)
      scale(1);
  }
}

/* ========================================================= */
/* РЕЗУЛЬТАТ */
/* ========================================================= */

.result-enter-active,
.result-leave-active {
  transition:
    opacity 0.35s ease,
    transform 0.35s ease;
}

.result-enter-from,
.result-leave-to {
  opacity: 0;
  transform: translateY(12px) scale(0.98);
}

/* ========================================================= */
/* МОБИЛЬНЫЙ ЭКРАН */
/* ========================================================= */

@media (max-width: 1023px) {
  .paw-wrapper {
    width: 85px;
    height: 85px;
  }

  .paw-svg {
    width: 85px;
    height: 85px;
  }
}

@media (max-width: 640px) {
  .paw-wrapper {
    right: -10px;
  }

  .paw-svg {
    width: 78px;
    height: 78px;
  }
}
</style>
