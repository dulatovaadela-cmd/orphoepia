```vue
<template>
  <div
    class="relative min-h-screen bg-gradient-to-br from-green-100 via-blue-50 to-purple-100 px-3 py-6 sm:px-4 sm:py-8 lg:px-8"
  >
    <!-- ========================================================= -->
    <!-- ОСНОВНАЯ ОБЛАСТЬ -->
    <!-- ========================================================= -->

    <div
      v-if="!finished"
      class="relative mx-auto w-full max-w-[1400px]"
    >
      <!-- ======================================================= -->
      <!-- ЗАДАНИЕ -->
      <!-- ======================================================= -->

      <div class="mx-auto max-w-5xl">
        <div
          class="rounded-2xl border-t-4 border-green-400 bg-white p-4 shadow-2xl sm:rounded-3xl sm:p-8"
        >
          <h2
            class="mb-2 break-words text-center text-2xl font-bold text-green-700 sm:text-3xl"
          >
            🌍 Ударения в заимствованных словах
          </h2>

          <p
            class="mb-6 text-center text-sm leading-relaxed text-gray-500 sm:text-base"
          >
            Определите, где падает ударение.
          </p>

          <div>
            <!-- ИНФОРМАЦИЯ О РАЗДЕЛЕ -->

            <div
              class="mb-4 break-words text-center text-base font-semibold leading-relaxed text-green-700 sm:text-lg"
            >
              Раздел {{ currentBlockIndex + 1 }} из {{ blocks.length }}:
              <b>{{ currentBlock.title }}</b>
            </div>

            <!-- ВОПРОСЫ -->

            <div v-if="!blockFinished">
              <div
                class="mb-4 break-words text-center text-lg leading-relaxed [overflow-wrap:anywhere] sm:text-xl"
                v-html="formattedSentence"
              ></div>

              <!-- ПОЛЕ ОТВЕТА -->

              <div class="relative mb-3">
                <input
                  v-model="userAnswer"
                  :disabled="showResult"
                  placeholder="Введите слово с правильным ударением"
                  @keyup.enter="checkAnswer"
                  class="w-full min-w-0 rounded-xl border border-gray-300 px-4 py-3 pr-16 text-base focus:outline-none focus:ring-2 focus:ring-green-400 sm:text-lg"
                />

                <!-- ЛАПКА ПРИ ПРАВИЛЬНОМ ОТВЕТЕ -->

                <Transition name="paw">
                  <div
                    v-if="showPaw && isCorrect"
                    class="pointer-events-none absolute right-1 top-1/2 z-20 -translate-y-1/2 sm:right-2"
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
                            id="furGradientBorrowings"
                            cx="35%"
                            cy="25%"
                            r="80%"
                          >
                            <stop
                              offset="0%"
                              stop-color="#f8ddb0"
                            />
                            <stop
                              offset="55%"
                              stop-color="#e5b978"
                            />
                            <stop
                              offset="100%"
                              stop-color="#b9783e"
                            />
                          </radialGradient>

                          <radialGradient
                            id="padGradientBorrowings"
                            cx="35%"
                            cy="30%"
                            r="75%"
                          >
                            <stop
                              offset="0%"
                              stop-color="#ffe5e8"
                            />
                            <stop
                              offset="60%"
                              stop-color="#f5b5c1"
                            />
                            <stop
                              offset="100%"
                              stop-color="#d98294"
                            />
                          </radialGradient>

                          <filter
                            id="pawShadowBorrowings"
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
                          fill="url(#furGradientBorrowings)"
                          stroke="#8b572f"
                          stroke-width="1.2"
                          filter="url(#pawShadowBorrowings)"
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

                          <ellipse
                            cx="47"
                            cy="54"
                            rx="25"
                            ry="28"
                          />
                        </g>

                        <!-- РОЗОВЫЕ ПОДУШЕЧКИ -->

                        <g
                          fill="url(#padGradientBorrowings)"
                          stroke="#b86d7d"
                          stroke-width="1"
                        >
                          <ellipse
                            cx="22"
                            cy="25"
                            rx="5"
                            ry="7"
                          />

                          <ellipse
                            cx="39"
                            cy="18"
                            rx="5"
                            ry="7"
                          />

                          <ellipse
                            cx="57"
                            cy="20"
                            rx="5"
                            ry="7"
                          />

                          <ellipse
                            cx="70"
                            cy="30"
                            rx="4.5"
                            ry="6.5"
                          />

                          <ellipse
                            cx="47"
                            cy="55"
                            rx="14"
                            ry="17"
                          />
                        </g>

                        <!-- ПЯТНА ЛЕОПАРДА -->

                        <g
                          fill="#7b4a2c"
                          opacity="0.9"
                        >
                          <circle
                            cx="15"
                            cy="18"
                            r="2.2"
                          />
                          <circle
                            cx="28"
                            cy="14"
                            r="2.5"
                          />
                          <circle
                            cx="35"
                            cy="28"
                            r="2"
                          />
                          <circle
                            cx="48"
                            cy="10"
                            r="2.2"
                          />
                          <circle
                            cx="63"
                            cy="14"
                            r="2.4"
                          />
                          <circle
                            cx="76"
                            cy="24"
                            r="2"
                          />
                          <circle
                            cx="19"
                            cy="42"
                            r="2.5"
                          />
                          <circle
                            cx="31"
                            cy="49"
                            r="2"
                          />
                          <circle
                            cx="63"
                            cy="43"
                            r="2.3"
                          />
                          <circle
                            cx="72"
                            cy="55"
                            r="2"
                          />
                          <circle
                            cx="35"
                            cy="69"
                            r="2.3"
                          />
                          <circle
                            cx="58"
                            cy="72"
                            r="2.5"
                          />
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

                      <span
                        class="paw-spark spark-one"
                      >
                        ✦
                      </span>

                      <span
                        class="paw-spark spark-two"
                      >
                        ✦
                      </span>

                      <span
                        class="paw-spark spark-three"
                      >
                        ✧
                      </span>
                    </div>
                  </div>
                </Transition>
              </div>

              <!-- РЕЗУЛЬТАТ -->

              <div
                v-if="showResult"
                class="text-center"
              >
                <p
                  v-if="isCorrect"
                  class="mb-2 font-semibold text-green-600"
                >
                  ✅ Верно!
                </p>

                <p
                  v-else
                  class="mb-2 break-words font-semibold leading-relaxed text-red-600"
                >
                  ❌ Неверно. Правильно:
                  <b>{{ currentQuestion.correct }}</b>
                </p>

                <!-- АУДИО -->

                <audio
                  v-if="currentQuestion.audio"
                  :src="currentQuestion.audio"
                  controls
                  class="mx-auto my-3 max-w-full"
                ></audio>

                <!-- СЛЕДУЮЩИЙ -->

                <button
                  @click="nextQuestion"
                  class="mt-3 w-full rounded-xl bg-purple-600 px-4 py-3 font-bold text-white transition hover:bg-purple-700"
                >
                  Следующий
                </button>
              </div>

              <!-- ПРОВЕРИТЬ -->

              <button
                v-else
                @click="checkAnswer"
                class="w-full rounded-xl bg-green-500 px-4 py-3 font-bold text-white transition hover:bg-green-600"
              >
                Проверить
              </button>

              <!-- НАЗАД / ВПЕРЁД -->

              <div
                class="mt-6 flex justify-between gap-3"
              >
                <button
                  @click="goPrev"
                  :disabled="globalIndex === 0"
                  class="min-w-0 flex-1 rounded-xl border border-gray-300 bg-white px-3 py-3 text-sm font-semibold text-gray-700 transition hover:bg-gray-100 disabled:opacity-40 sm:px-6 sm:text-base"
                >
                  ⬅ Назад
                </button>

                <button
                  @click="goNext"
                  :disabled="
                    globalIndex ===
                    totalQuestions - 1
                  "
                  class="min-w-0 flex-1 rounded-xl border border-gray-300 bg-white px-3 py-3 text-sm font-semibold text-gray-700 transition hover:bg-gray-100 disabled:opacity-40 sm:px-6 sm:text-base"
                >
                  Вперёд ➡
                </button>
              </div>
            </div>

            <!-- ПРАВИЛО ПОСЛЕ БЛОКА -->

            <div
              v-else
              class="mt-6 rounded-xl border border-green-200 bg-green-50 p-4 text-center sm:p-6"
            >
              <p
                class="mb-2 text-xl font-semibold text-green-700"
              >
                📘 Подсказка:
              </p>

              <p
                class="break-words leading-relaxed text-gray-700 italic"
              >
                {{ currentBlock.rule }}
              </p>

              <button
                @click="nextBlock"
                class="mt-4 w-full rounded-xl bg-purple-600 px-6 py-3 font-bold text-white transition hover:bg-purple-700 sm:w-auto"
              >
                Далее
              </button>
            </div>
          </div>
        </div>
      </div>

      <!-- ======================================================= -->
      <!-- ОКНО НУМЕРАЦИИ -->
      <!-- ======================================================= -->

      <div
        class="mt-6 w-full rounded-[26px] border-2 border-[#d7b98b] bg-[#f7ead2] p-4 shadow-xl sm:p-5 lg:absolute lg:right-0 lg:top-0 lg:mt-0 lg:w-72"
      >
        <h3
          class="mb-4 text-center font-bold text-[#795548]"
        >
          Вопросы
        </h3>

        <div
          class="grid grid-cols-5 gap-2"
        >
          <button
            v-for="(q, idx) in totalQuestions"
            :key="idx"
            @click="jumpTo(idx)"
            class="flex h-10 w-full items-center justify-center rounded-xl border-2 text-sm font-bold transition-all duration-200 sm:h-11 sm:text-base"
            :class="{
              'scale-105 border-blue-600 bg-blue-500 text-white shadow-lg':
                idx === globalIndex,

              'border-green-600 bg-green-500 text-white shadow-md':
                answered[idx] &&
                idx !== globalIndex,

              'border-[#d7b98b] bg-[#fff8eb] text-[#795548] hover:scale-105 hover:bg-white':
                !answered[idx] &&
                idx !== globalIndex
            }"
          >
            {{ idx + 1 }}
          </button>
        </div>
      </div>
    </div>

    <!-- ========================================================= -->
    <!-- ФИНАЛ -->
    <!-- ========================================================= -->

    <div
      v-if="finished"
      class="mx-auto w-full max-w-2xl rounded-2xl border-t-4 border-green-400 bg-white p-4 shadow-2xl sm:p-8"
    >
      <div class="mt-2 text-center sm:mt-6">
        <div
          class="w-full rounded-xl bg-white p-5 shadow-lg sm:p-6"
        >
          <p
            class="mb-2 text-2xl font-bold text-green-700 sm:text-3xl"
          >
            🎉 Ваши результаты
          </p>

          <p
            class="mb-2 text-base leading-relaxed sm:text-lg"
          >
            Вы выполнили задание
            «Ударения в заимствованных словах»
          </p>

          <p
            class="mb-2 text-3xl font-extrabold text-green-600 sm:text-4xl"
          >
            {{ correctCount }}/{{ totalQuestions }}
          </p>

          <p
            class="mb-2 text-2xl font-bold text-blue-600"
          >
            {{ percent }}%
          </p>

          <p
            class="mb-4 text-xl font-bold"
          >
            Оценка: {{ grade }}
          </p>

          <p
            v-if="saveStatus === 'saving'"
            class="mb-4 text-gray-500"
          >
            ⏳ Сохраняем результат...
          </p>

          <p
            v-else-if="saveStatus === 'saved'"
            class="mb-4 font-semibold text-green-600"
          >
            ✅ Результат сохранён
          </p>

          <p
            v-else-if="saveStatus === 'error'"
            class="mb-4 font-semibold text-red-600"
          >
            ⚠️ Не удалось сохранить результат
          </p>

          <div
            class="flex flex-col gap-3"
          >
            <NuxtLink
              to="/results"
              class="block rounded-lg bg-purple-600 px-6 py-3 font-bold text-white transition hover:bg-purple-700"
            >
              📊 Мой прогресс
            </NuxtLink>

            <NuxtLink
              to="/"
              class="mt-2 block text-blue-600 underline hover:text-blue-800"
            >
              ⬅ На главную
            </NuxtLink>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import {
  ref,
  computed,
  onMounted,
  onBeforeUnmount
} from 'vue'

interface Question {
  sentence: string
  correct: string
  audio?: string
}

interface Block {
  title: string
  questions: Question[]
  rule: string
}

interface Student {
  name: string
  class: string
}

const supabase = useSupabaseClient()

const blocks: Block[] = [
  {
    title: "🇫🇷 Французские заимствования",
    rule:
      "Слова из французского языка обычно имеют ударение на последнем слоге.",
    questions: [
      {
        sentence:
          "Портмоне лежало на столе.",
        correct: "портмонЕ"
      },
      {
        sentence:
          "Платье из ткани гофре.",
        correct: "гофрЕ"
      },
      {
        sentence:
          "Выступал квартет.",
        correct: "квартЕт"
      },
      {
        sentence:
          "Поезда стояли в депо.",
        correct: "депО"
      },
      {
        sentence:
          "Она опустила жалюзи.",
        correct: "жалюзИ"
      }
    ]
  },

  {
    title: "🇬🇧 Английские заимствования",
    rule:
      "В английских словах ударение часто на первом слоге.",
    questions: [
      {
        sentence:
          "Менеджмент — это наука.",
        correct: "мЕнеджмент"
      },
      {
        sentence:
          "Команда прошла тренинг.",
        correct: "трЕнинг"
      },
      {
        sentence:
          "Учитель включил таймер.",
        correct: "тАймер"
      },
      {
        sentence:
          "Маркетинг важен.",
        correct: "мАркетинг"
      },
      {
        sentence:
          "Ошибка на сервере.",
        correct: "сЕрвере"
      }
    ]
  },

  {
    title: "🇬🇷 Латинские и греческие",
    rule:
      "Ударение в латинских словах часто на одном из последних трёх слогов.",
    questions: [
      {
        sentence:
          "Алюминиевые рамы.",
        correct: "алюмИниевые"
      },
      {
        sentence:
          "Вставьте апостроф.",
        correct: "апострОф"
      },
      {
        sentence:
          "Учёный выдвинул гипотезу.",
        correct: "гипОтезу"
      },
      {
        sentence:
          "Школьный бюллетень.",
        correct: "бюллетЕнь"
      },
      {
        sentence:
          "Он пошёл в стоматологию.",
        correct: "стоматолОгия"
      }
    ]
  }
]

/* СОСТОЯНИЕ */

const currentBlockIndex = ref(0)
const currentQuestionIndex = ref(0)

const totalQuestions =
  blocks.reduce(
    (sum, block) =>
      sum + block.questions.length,
    0
  )

const answered = ref<boolean[]>(
  Array(totalQuestions).fill(false)
)

const userAnswer = ref("")
const showResult = ref(false)
const isCorrect = ref(false)
const finished = ref(false)
const blockFinished = ref(false)
const correctCount = ref(0)

/* ЛАПКА */

const showPaw = ref(false)

let pawTimer:
  | ReturnType<typeof setTimeout>
  | null = null

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

/* ТЕКУЩИЙ ВОПРОС */

const globalIndex = computed(() => {
  let sum = 0

  for (
    let i = 0;
    i < currentBlockIndex.value;
    i++
  ) {
    sum +=
      blocks[i].questions.length
  }

  return (
    sum + currentQuestionIndex.value
  )
})

const currentBlock = computed(
  () => blocks[currentBlockIndex.value]!
)

const currentQuestion = computed(
  () =>
    currentBlock.value.questions[
      currentQuestionIndex.value
    ]!
)

/* ПРЕДЛОЖЕНИЕ */

const formattedSentence = computed(() => {
  const sentence =
    currentQuestion.value.sentence

  const correct =
    currentQuestion.value.correct

  const clean = correct.replace(
    /[Ёё]/g,
    (m) => m.toUpperCase()
  )

  const cleanWord = clean.replace(
    /[^А-Яа-яЁё]/g,
    ""
  )

  const reg = new RegExp(
    cleanWord,
    "i"
  )

  return sentence.replace(
    reg,
    (match) => `<b>${match}</b>`
  )
})

/* РЕЗУЛЬТАТЫ */

const percent = computed(() =>
  Math.round(
    (correctCount.value /
      totalQuestions) *
      100
  )
)

const grade = computed(() => {
  if (percent.value < 50) return 2
  if (percent.value <= 70) return 3
  if (percent.value <= 84) return 4
  return 5
})

/* УЧЕНИК */

const studentName = ref("")
const studentClass = ref("")

const attemptId =
  ref<string | number | null>(null)

const startedAt = ref("")

const saveStatus = ref<
  "saving" | "saved" | "error"
>("saving")

/* СОЗДАНИЕ ПОПЫТКИ */

onMounted(async () => {
  const savedStudent =
    localStorage.getItem(
      "orphoepia_student"
    )

  if (!savedStudent) {
    await navigateTo("/")
    return
  }

  try {
    const student: Student =
      JSON.parse(savedStudent)

    if (
      !student.name?.trim() ||
      !student.class?.trim()
    ) {
      await navigateTo("/")
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
        .from("attempts")
        .insert({
          student_name:
            studentName.value,

          class:
            studentClass.value,

          task_name:
            "Ударения в заимствованных словах",

          total_questions:
            totalQuestions,

          started_at:
            startedAt.value
        })
        .select("id")
        .single()

    if (error) {
      console.error(
        "Ошибка создания попытки:",
        error
      )

      saveStatus.value = "error"
      return
    }

    attemptId.value = data.id
    saveStatus.value = "saving"
  } catch (error) {
    console.error(
      "Ошибка чтения данных ученика:",
      error
    )

    await navigateTo("/")
  }
})

/* СОХРАНЕНИЕ РЕЗУЛЬТАТА */

async function saveResult() {
  if (!attemptId.value) {
    saveStatus.value = "error"
    return
  }

  saveStatus.value = "saving"

  const completedAt =
    new Date().toISOString()

  const { error } =
    await supabase
      .from("attempts")
      .update({
        score: correctCount.value,
        percentage: percent.value,
        completed_at: completedAt
      })
      .eq(
        "id",
        attemptId.value
      )

  if (error) {
    console.error(
      "Ошибка сохранения результата:",
      error
    )

    saveStatus.value = "error"
    return
  }

  saveStatus.value = "saved"
}

/* ПРОВЕРКА */

function checkAnswer() {
  if (!userAnswer.value.trim())
    return

  if (showResult.value)
    return

  showResult.value = true

  isCorrect.value =
    userAnswer.value
      .trim()
      .toLowerCase() ===
    currentQuestion.value.correct
      .toLowerCase()

  answered.value[
    globalIndex.value
  ] = true

  if (isCorrect.value) {
    correctCount.value++
    triggerCorrectPaw()
  }
}

/* СЛЕДУЮЩИЙ ВОПРОС */

function nextQuestion() {
  showPaw.value = false

  if (pawTimer) {
    clearTimeout(pawTimer)
    pawTimer = null
  }

  if (
    currentQuestionIndex.value + 1 <
    currentBlock.value.questions.length
  ) {
    currentQuestionIndex.value++
  } else {
    blockFinished.value = true
  }

  userAnswer.value = ""
  showResult.value = false
  isCorrect.value = false
}

/* СЛЕДУЮЩИЙ БЛОК */

async function nextBlock() {
  showPaw.value = false

  if (pawTimer) {
    clearTimeout(pawTimer)
    pawTimer = null
  }

  if (
    currentBlockIndex.value + 1 <
    blocks.length
  ) {
    currentBlockIndex.value++
    currentQuestionIndex.value = 0
    blockFinished.value = false
    userAnswer.value = ""
    showResult.value = false
    isCorrect.value = false
  } else {
    finished.value = true
    await saveResult()
  }
}

/* НАЗАД */

function goPrev() {
  if (globalIndex.value === 0)
    return

  jumpTo(
    globalIndex.value - 1
  )
}

/* ВПЕРЁД */

function goNext() {
  if (
    globalIndex.value ===
    totalQuestions - 1
  ) {
    return
  }

  jumpTo(
    globalIndex.value + 1
  )
}

/* ПЕРЕХОД К ВОПРОСУ */

function jumpTo(target: number) {
  showPaw.value = false

  if (pawTimer) {
    clearTimeout(pawTimer)
    pawTimer = null
  }

  let acc = 0

  for (
    let i = 0;
    i < blocks.length;
    i++
  ) {
    const size =
      blocks[i].questions.length

    if (target < acc + size) {
      currentBlockIndex.value = i

      currentQuestionIndex.value =
        target - acc

      break
    }

    acc += size
  }

  blockFinished.value = false
  userAnswer.value = ""
  showResult.value = false
  isCorrect.value = false
}

/* ОЧИСТКА ТАЙМЕРА */

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

b {
  color: #10b981;
}

/* ========================================================= */
/* ЛАПКА */
/* ========================================================= */

.paw-svg {
  width: 48px;
  height: 48px;
  display: block;
  transform-origin: 70% 80%;
  animation:
    paw-life 1.1s ease-in-out infinite;
}

.paw-spark {
  position: absolute;
  pointer-events: none;
  color: #d79a4d;
  font-weight: 900;
  text-shadow:
    0 1px 3px
    rgba(130, 75, 30, 0.2);
}

.spark-one {
  top: 3px;
  right: 2px;
  font-size: 12px;
  animation:
    sparkle-one 1s ease-in-out infinite;
}

.spark-two {
  top: 18px;
  right: -2px;
  font-size: 9px;
  animation:
    sparkle-two 1.2s ease-in-out infinite;
}

.spark-three {
  bottom: 5px;
  left: 1px;
  font-size: 10px;
  animation:
    sparkle-three 1.1s ease-in-out infinite;
}

/* ========================================================= */
/* ПОЯВЛЕНИЕ ЛАПКИ */
/* ========================================================= */

.paw-enter-active,
.paw-leave-active {
  transition:
    opacity 0.45s ease,
    transform 0.45s
      cubic-bezier(0.2, 0.8, 0.2, 1);
}

.paw-enter-from {
  opacity: 0;
  transform:
    translate(18px, -50%)
    rotate(18deg)
    scale(0.65);
}

.paw-enter-to {
  opacity: 1;
  transform:
    translate(0, -50%)
    rotate(0deg)
    scale(1);
}

.paw-leave-from {
  opacity: 1;
  transform:
    translate(0, -50%)
    rotate(0deg)
    scale(1);
}

.paw-leave-to {
  opacity: 0;
  transform:
    translate(18px, -50%)
    rotate(14deg)
    scale(0.7);
}

/* ========================================================= */
/* ЖИВОЕ ДВИЖЕНИЕ */
/* ========================================================= */

@keyframes paw-life {
  0%,
  100% {
    transform:
      rotate(-3deg)
      translateY(0);
  }

  50% {
    transform:
      rotate(4deg)
      translateY(-2px);
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

/* ========================================================= */
/* МАЛЕНЬКИЙ ТЕЛЕФОН */
/* ========================================================= */

@media (max-width: 360px) {
  .paw-svg {
    width: 44px;
    height: 44px;
  }
}
</style>
```
