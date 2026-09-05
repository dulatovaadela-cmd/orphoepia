<template>
  <div
    class="relative min-h-screen overflow-x-hidden bg-gradient-to-br from-[#fff4df] via-[#fce8d5] to-[#f7d9c8] py-6 px-3 sm:py-8 sm:px-4"
  >
    <!-- ОКОШКО НУМЕРАЦИИ -->
    <div
      v-if="!finished"
      class="mx-auto mt-4 w-full max-w-2xl rounded-2xl border-2 border-[#d7b98b] bg-[#f7ead2] p-3 shadow-[0_8px_25px_rgba(100,70,30,0.18)] sm:p-4 md:absolute md:right-5 md:top-5 md:mt-0 md:w-72"
    >
      <div class="mb-2 text-center text-xs font-bold uppercase tracking-wider text-[#8b6840]">
        Вопросы
      </div>

      <div class="grid grid-cols-5 gap-2 sm:gap-3">
        <button
          v-for="(q, i) in quiz"
          :key="i"
          @click="jumpTo(i)"
          class="flex h-9 w-full items-center justify-center rounded-lg border-2 text-sm font-extrabold transition-all duration-200 sm:h-10 sm:text-base"
          :class="[
            currentIdx === i
              ? 'border-blue-600 bg-blue-500 text-white shadow-lg scale-105'
              : answered[i]
                ? 'border-green-600 bg-green-500 text-white shadow-md'
                : 'border-[#d6c09c] bg-[#fffaf1] text-[#765a39] hover:bg-[#fff1d5] hover:scale-105'
          ]"
        >
          {{ i + 1 }}
        </button>
      </div>
    </div>

    <!-- ОСНОВНОЙ БЛОК -->
    <div
      class="mx-auto w-full max-w-2xl rounded-[28px] border-2 border-[#e3c6a8] bg-white/95 p-4 shadow-[0_15px_45px_rgba(110,70,40,0.18)] sm:p-6 md:p-8"
    >
      <h2
        class="mb-2 text-center text-2xl font-black text-[#8d4f32] sm:text-3xl"
      >
        🎯 Викторина: Ударения
      </h2>

      <p class="mb-5 text-center text-sm font-medium text-[#8a7665] sm:mb-6 sm:text-base">
        Выберите правильный вариант ударения
      </p>

      <!-- ВИКТОРИНА -->
      <div v-if="!finished || showPaw">

        <div
          class="mb-4 text-center text-base font-extrabold text-[#4775a8] sm:text-lg"
        >
          Вопрос {{ currentIdx + 1 }} из {{ quiz.length }}
        </div>

        <div
          class="mb-5 break-words rounded-2xl bg-[#fff7e8] px-4 py-4 text-center text-2xl font-black text-[#8d4f32] shadow-inner sm:mb-6 sm:text-3xl"
        >
          {{ currentQuestion.word }}
        </div>

        <!-- ВАРИАНТЫ -->
        <div class="mb-6 flex flex-col gap-4">
          <button
            v-for="(option, idx) in currentQuestion.options"
            :key="idx"
            :disabled="showResult"
            @click="selectOption(idx)"
            class="answer-button relative min-h-[62px] w-full min-w-0 overflow-visible rounded-2xl border-2 py-3 pl-5 pr-20 text-left text-base font-bold transition-all duration-300 sm:text-lg"
            :class="[
              !showResult && selectedIdx !== idx
                ? 'border-[#d8c8b9] bg-white text-[#57483d] hover:-translate-y-1 hover:border-[#c49a72] hover:bg-[#fffaf3] hover:shadow-lg'
                : '',

              showResult && idx === currentQuestion.correct
                ? 'correct-answer border-green-600 text-green-950 shadow-[0_0_0_3px_rgba(34,197,94,0.18),0_10px_30px_rgba(34,197,94,0.25)]'
                : '',

              showResult &&
              selectedIdx === idx &&
              selectedIdx !== currentQuestion.correct
                ? 'wrong-answer border-red-500 bg-red-100 text-red-900 shadow-lg'
                : '',

              showResult &&
              selectedIdx !== idx &&
              idx !== currentQuestion.correct
                ? 'border-[#ded6ce] bg-gray-50 text-gray-500'
                : ''
            ]"
          >
            <span
              class="relative z-10 block break-words whitespace-normal leading-relaxed [overflow-wrap:anywhere]"
            >
              {{ option }}
            </span>

            <!-- ЗНАЧОК ПРАВИЛЬНОГО ОТВЕТА -->
            <span
              v-if="showResult && idx === currentQuestion.correct"
              class="absolute right-4 top-1/2 z-10 flex h-9 w-9 -translate-y-1/2 items-center justify-center rounded-full bg-green-600 text-xl text-white shadow-lg"
            >
              ✓
            </span>

            <!-- ЛЕОПАРДОВАЯ ЛАПКА -->
            <Transition name="paw">
              <div
                v-if="
                  showPaw &&
                  idx === currentQuestion.correct &&
                  selectedIdx === currentQuestion.correct
                "
                class="paw-container pointer-events-none absolute -right-5 top-1/2 z-30 -translate-y-1/2 sm:-right-7"
                aria-hidden="true"
              >
                <div class="paw-motion">
                  <svg
                    viewBox="0 0 120 120"
                    class="paw-svg"
                    xmlns="http://www.w3.org/2000/svg"
                  >
                    <defs>
                      <radialGradient
                        id="furGradientQuiz"
                        cx="30%"
                        cy="20%"
                        r="90%"
                      >
                        <stop offset="0%" stop-color="#fff0c9" />
                        <stop offset="30%" stop-color="#edc078" />
                        <stop offset="65%" stop-color="#c98542" />
                        <stop offset="100%" stop-color="#8d512c" />
                      </radialGradient>

                      <radialGradient
                        id="padGradientQuiz"
                        cx="35%"
                        cy="25%"
                        r="80%"
                      >
                        <stop offset="0%" stop-color="#ffd9df" />
                        <stop offset="55%" stop-color="#ee9daa" />
                        <stop offset="100%" stop-color="#bd6577" />
                      </radialGradient>

                      <linearGradient
                        id="clawGradientQuiz"
                        x1="0"
                        y1="0"
                        x2="1"
                        y2="1"
                      >
                        <stop offset="0%" stop-color="#fff8e8" />
                        <stop offset="100%" stop-color="#d8a66c" />
                      </linearGradient>

                      <filter
                        id="pawShadowQuiz"
                        x="-40%"
                        y="-40%"
                        width="190%"
                        height="190%"
                      >
                        <feDropShadow
                          dx="0"
                          dy="6"
                          stdDeviation="4"
                          flood-color="#5b351e"
                          flood-opacity="0.4"
                        />
                      </filter>

                      <filter
                        id="pawGlowQuiz"
                        x="-50%"
                        y="-50%"
                        width="200%"
                        height="200%"
                      >
                        <feGaussianBlur stdDeviation="3" />
                      </filter>
                    </defs>

                    <!-- СВЕЧЕНИЕ -->
                    <ellipse
                      cx="62"
                      cy="65"
                      rx="42"
                      ry="45"
                      fill="#ffd76a"
                      opacity="0.22"
                      filter="url(#pawGlowQuiz)"
                    />

                    <!-- ПАЛЬЦЫ -->
                    <g
                      fill="url(#furGradientQuiz)"
                      stroke="#754322"
                      stroke-width="1.7"
                      filter="url(#pawShadowQuiz)"
                    >
                      <ellipse
                        cx="24"
                        cy="30"
                        rx="11"
                        ry="17"
                        transform="rotate(-28 24 30)"
                      />
                      <ellipse
                        cx="46"
                        cy="19"
                        rx="11"
                        ry="18"
                        transform="rotate(-10 46 19)"
                      />
                      <ellipse
                        cx="69"
                        cy="21"
                        rx="11"
                        ry="18"
                        transform="rotate(10 69 21)"
                      />
                      <ellipse
                        cx="91"
                        cy="34"
                        rx="10"
                        ry="17"
                        transform="rotate(27 91 34)"
                      />

                      <!-- ЛАДОНЬ -->
                      <ellipse cx="59" cy="70" rx="34" ry="38" />
                    </g>

                    <!-- ПОДУШЕЧКИ -->
                    <g
                      fill="url(#padGradientQuiz)"
                      stroke="#a95769"
                      stroke-width="1.4"
                    >
                      <ellipse cx="24" cy="30" rx="6.5" ry="9" />
                      <ellipse cx="46" cy="20" rx="6.5" ry="9" />
                      <ellipse cx="69" cy="22" rx="6.5" ry="9" />
                      <ellipse cx="91" cy="34" rx="6" ry="8.5" />
                      <ellipse cx="59" cy="70" rx="19" ry="23" />
                    </g>

                    <!-- ЛЕОПАРДОВЫЕ ПЯТНА -->
                    <g fill="#63371f">
                      <circle cx="17" cy="22" r="3" />
                      <circle cx="31" cy="15" r="2.6" />
                      <circle cx="39" cy="32" r="2.8" />
                      <circle cx="54" cy="9" r="3" />
                      <circle cx="76" cy="12" r="2.8" />
                      <circle cx="97" cy="26" r="3" />
                      <circle cx="21" cy="51" r="3.2" />
                      <circle cx="38" cy="54" r="2.5" />
                      <circle cx="77" cy="47" r="3" />
                      <circle cx="92" cy="57" r="2.6" />
                      <circle cx="35" cy="78" r="2.8" />
                      <circle cx="49" cy="92" r="3" />
                      <circle cx="72" cy="88" r="2.8" />
                      <circle cx="83" cy="73" r="2.5" />
                    </g>

                    <!-- МЕЛКИЕ КОГТИ -->
                    <g
                      fill="url(#clawGradientQuiz)"
                      opacity="0.9"
                    >
                      <ellipse
                        cx="18"
                        cy="12"
                        rx="2.5"
                        ry="6"
                        transform="rotate(-20 18 12)"
                      />
                      <ellipse
                        cx="45"
                        cy="2"
                        rx="2.5"
                        ry="6"
                      />
                      <ellipse
                        cx="74"
                        cy="4"
                        rx="2.5"
                        ry="6"
                        transform="rotate(15 74 4)"
                      />
                      <ellipse
                        cx="101"
                        cy="18"
                        rx="2.5"
                        ry="6"
                        transform="rotate(25 101 18)"
                      />
                    </g>

                    <!-- БЛИК -->
                    <ellipse
                      cx="48"
                      cy="57"
                      rx="8"
                      ry="12"
                      fill="#fff"
                      opacity="0.3"
                    />
                  </svg>

                  <span class="paw-spark spark-one">✦</span>
                  <span class="paw-spark spark-two">✦</span>
                  <span class="paw-spark spark-three">✦</span>
                  <span class="paw-spark spark-four">•</span>
                </div>
              </div>
            </Transition>
          </button>
        </div>

        <!-- РЕЗУЛЬТАТ ОТВЕТА -->
        <div
          v-if="showResult"
          class="mt-5 flex flex-col gap-4 text-center"
        >
          <div
            class="rounded-2xl border-2 p-4"
            :class="
              selectedIdx === currentQuestion.correct
                ? 'border-green-300 bg-green-50'
                : 'border-red-300 bg-red-50'
            "
          >
            <p
              v-if="selectedIdx === currentQuestion.correct"
              class="mb-2 text-lg font-extrabold text-green-700"
            >
              ✅ Правильно!
            </p>

            <p
              v-else
              class="mb-2 font-extrabold text-red-600"
            >
              ❌ Неверно.
            </p>

            <p
              v-if="selectedIdx !== currentQuestion.correct"
              class="mb-3 text-sm text-red-700 sm:text-base"
            >
              Правильный ответ:
              <b>
                {{ currentQuestion.options[currentQuestion.correct] }}
              </b>
            </p>

            <p class="mb-3 mt-3 break-words text-sm text-gray-700 sm:text-base">
              📘
              <span class="italic">{{ currentQuestion.rule }}</span>
            </p>

            <audio
              v-if="currentQuestion.audio"
              :src="currentQuestion.audio"
              controls
              class="mx-auto my-2 max-w-full"
            ></audio>
          </div>

          <!-- КНОПКИ НАВИГАЦИИ -->
          <div
            class="flex flex-col gap-3 sm:flex-row sm:justify-between sm:gap-4"
          >
            <button
              @click="prevQuestion"
              :disabled="currentIdx === 0"
              class="w-full rounded-xl bg-gray-300 px-4 py-3 font-bold text-gray-800 shadow-md transition hover:bg-gray-400 disabled:opacity-50 sm:flex-1"
            >
              ⬅️ Назад
            </button>

            <button
              @click="nextQuestion"
              :disabled="currentIdx >= quiz.length - 1"
              class="w-full rounded-xl bg-[#9b5b3e] px-4 py-3 font-bold text-white shadow-md transition hover:bg-[#82482f] disabled:opacity-50 sm:flex-1"
            >
              Вперёд ➡️
            </button>
          </div>
        </div>
      </div>

      <!-- ФИНАЛЬНЫЙ РЕЗУЛЬТАТ -->
      <div v-else class="mt-6 text-center">
        <div
          class="rounded-3xl border-2 border-[#e2c9a9] bg-[#fff5e5] p-4 shadow-lg sm:p-8"
        >
          <p
            class="mb-3 text-2xl font-extrabold text-[#8d4f32] sm:text-3xl"
          >
            🎉 Ваши результаты
          </p>

          <p class="mb-6 text-base text-gray-600 sm:text-lg">
            Вы выполнили задание «Викторина»
          </p>

          <div
            class="mb-5 rounded-2xl bg-white p-5 shadow-md sm:p-6"
          >
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
              class="block rounded-xl bg-[#9b5b3e] px-5 py-3 font-semibold text-white shadow-md transition hover:bg-[#82482f]"
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
  {
    word: 'каталог',
    options: ['катАлог', 'каталОг'],
    correct: 1,
    rule: 'В словах на -лог ударение падает на последний слог.',
    audio: '/audio/katalog.ogg'
  },
  {
    word: 'свёкла',
    options: ['свеклА', 'свЁкла'],
    correct: 1,
    rule: 'Ударение на первом слоге.',
    audio: '/audio/svekla.ogg'
  },
  {
    word: 'тортов',
    options: ['тОртов', 'тортОв'],
    correct: 0,
    rule: 'Ударение сохраняется на основе.',
    audio: '/audio/tortov.ogg'
  },
  {
    word: 'заняла',
    options: ['занялА', 'зАняла'],
    correct: 0,
    rule: 'Ударение в ж.р. на окончание.',
    audio: '/audio/zanyala.ogg'
  },
  {
    word: 'щавель',
    options: ['щАвель', 'щавЕль'],
    correct: 1,
    rule: 'Ударение на последний слог.',
    audio: '/audio/shavel.ogg'
  },
  {
    word: 'квартал',
    options: ['квАртал', 'квартАл'],
    correct: 1,
    rule: 'Ударение в -ал на последний слог.',
    audio: '/audio/kvartal.ogg'
  },
  {
    word: 'кремень',
    options: ['крЕмень', 'кремЕнь'],
    correct: 1,
    rule: 'Ударение в -ень на последний слог.',
    audio: '/audio/kremen.ogg'
  },
  {
    word: 'жалюзи',
    options: ['жАлюзи', 'жалюзИ'],
    correct: 1,
    rule: 'Французские слова — ударение на конце.',
    audio: '/audio/zhaluzi.ogg'
  },
  {
    word: 'позвонишь',
    options: ['позвОнишь', 'позвонИшь'],
    correct: 1,
    rule: 'Ударение в глаголах на окончание.',
    audio: '/audio/pozvovish.ogg'
  },
  {
    word: 'красивее',
    options: ['крАсивее', 'красИвее'],
    correct: 1,
    rule: 'Ударение на -и- в сравнительной степени.',
    audio: '/audio/krasivee.ogg'
  },
  {
    word: 'начав',
    options: ['нАчав', 'начАв'],
    correct: 1,
    rule: 'В деепричастиях ударение на окончание.',
    audio: '/audio/nachav.ogg'
  },
  {
    word: 'баловать',
    options: ['бАловать', 'баловАть'],
    correct: 1,
    rule: 'В -овать ударение на окончание.',
    audio: '/audio/balovat.ogg'
  },
  {
    word: 'краны',
    options: ['крАны', 'кранЫ'],
    correct: 0,
    rule: 'В муж. р. ударение на первом слоге.',
    audio: '/audio/krany.ogg'
  },
  {
    word: 'включим',
    options: ['вклЮчим', 'включИм'],
    correct: 1,
    rule: 'Ударение на окончание.',
    audio: '/audio/vkluchim.ogg'
  },
  {
    word: 'создала',
    options: ['сОздала', 'создалА'],
    correct: 1,
    rule: 'В ж.р. ударение на окончание.',
    audio: '/audio/sozdala.ogg'
  }
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

  showPaw.value = false

  requestAnimationFrame(() => {
    requestAnimationFrame(() => {
      showPaw.value = true
    })
  })

  pawTimer = setTimeout(() => {
    showPaw.value = false
    pawTimer = null
  }, 3600)
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
  transform: translateY(-1px);
}

.answer-button {
  isolation: isolate;
}

/* ЯРКИЙ ПРАВИЛЬНЫЙ ОТВЕТ */
.correct-answer {
  background: linear-gradient(
    135deg,
    #dcfce7 0%,
    #bbf7d0 45%,
    #86efac 100%
  );
  animation: correct-pulse 1.5s ease-in-out infinite;
}

/* НЕПРАВИЛЬНЫЙ ОТВЕТ */
.wrong-answer {
  animation: wrong-shake 0.45s ease-in-out;
}

/* ЛАПКА */
.paw-container {
  width: 108px;
  height: 108px;
}

.paw-motion {
  position: relative;
  width: 100%;
  height: 100%;
  animation: paw-pull 0.8s ease-in-out infinite;
  transform-origin: 80% 70%;
}

.paw-svg {
  width: 108px;
  height: 108px;
  display: block;
  transform-origin: 75% 70%;
  animation: paw-wiggle 0.65s ease-in-out infinite;
  filter: drop-shadow(0 8px 8px rgba(90, 45, 20, 0.28));
}

/* ЛАПКА БУДТО ТЯНЕТ ОТВЕТ */
@keyframes paw-pull {
  0% {
    transform: translateX(18px) rotate(12deg) scale(0.85);
  }

  30% {
    transform: translateX(-7px) rotate(-7deg) scale(1.08);
  }

  55% {
    transform: translateX(3px) rotate(5deg) scale(1);
  }

  75% {
    transform: translateX(-4px) rotate(-4deg) scale(1.04);
  }

  100% {
    transform: translateX(18px) rotate(12deg) scale(0.85);
  }
}

@keyframes paw-wiggle {
  0%,
  100% {
    transform: rotate(-5deg);
  }

  50% {
    transform: rotate(7deg);
  }
}

/* ЗЕЛЁНЫЙ ОТВЕТ ПУЛЬСИРУЕТ */
@keyframes correct-pulse {
  0%,
  100% {
    box-shadow:
      0 0 0 2px rgba(34, 197, 94, 0.12),
      0 8px 22px rgba(34, 197, 94, 0.18);
  }

  50% {
    box-shadow:
      0 0 0 5px rgba(34, 197, 94, 0.2),
      0 12px 30px rgba(34, 197, 94, 0.3);
  }
}

/* ОШИБКА */
@keyframes wrong-shake {
  0%,
  100% {
    transform: translateX(0);
  }

  20% {
    transform: translateX(-7px);
  }

  40% {
    transform: translateX(7px);
  }

  60% {
    transform: translateX(-5px);
  }

  80% {
    transform: translateX(5px);
  }
}

/* ИСКРЫ */
.paw-spark {
  position: absolute;
  pointer-events: none;
  color: #d18a25;
  font-weight: 900;
  line-height: 1;
  text-shadow:
    0 1px 3px rgba(120, 65, 20, 0.35),
    0 0 8px rgba(255, 196, 70, 0.6);
}

.spark-one {
  top: 2px;
  right: 0;
  font-size: 20px;
  animation: sparkle-one 0.8s ease-in-out infinite;
}

.spark-two {
  top: 30px;
  right: -5px;
  font-size: 15px;
  animation: sparkle-two 0.95s ease-in-out infinite;
}

.spark-three {
  bottom: 15px;
  left: -4px;
  font-size: 18px;
  animation: sparkle-three 0.75s ease-in-out infinite;
}

.spark-four {
  top: 52px;
  left: -7px;
  font-size: 12px;
  animation: sparkle-four 0.7s ease-in-out infinite;
}

@keyframes sparkle-one {
  0%,
  100% {
    opacity: 0.2;
    transform: scale(0.6) rotate(0deg);
  }

  50% {
    opacity: 1;
    transform: scale(1.35) rotate(25deg);
  }
}

@keyframes sparkle-two {
  0%,
  100% {
    opacity: 0.15;
    transform: translateY(3px) scale(0.7);
  }

  50% {
    opacity: 1;
    transform: translateY(-6px) scale(1.2);
  }
}

@keyframes sparkle-three {
  0%,
  100% {
    opacity: 0.2;
    transform: scale(0.6) rotate(0deg);
  }

  50% {
    opacity: 1;
    transform: scale(1.3) rotate(-20deg);
  }
}

@keyframes sparkle-four {
  0%,
  100% {
    opacity: 0.1;
    transform: translateX(2px);
  }

  50% {
    opacity: 1;
    transform: translateX(-5px);
  }
}

/* ПОЯВЛЕНИЕ ЛАПКИ */
.paw-enter-active {
  animation: paw-enter 0.55s cubic-bezier(0.15, 0.85, 0.25, 1.2);
}

.paw-leave-active {
  transition: opacity 0.25s ease;
}

.paw-enter-from,
.paw-leave-to {
  opacity: 0;
}

@keyframes paw-enter {
  0% {
    opacity: 0;
    transform: translateX(70px) translateY(-50%) rotate(25deg)
      scale(0.35);
  }

  45% {
    opacity: 1;
    transform: translateX(-15px) translateY(-50%) rotate(-12deg)
      scale(1.18);
  }

  70% {
    transform: translateX(5px) translateY(-50%) rotate(7deg)
      scale(0.98);
  }

  100% {
    opacity: 1;
    transform: translateX(0) translateY(-50%) rotate(0deg)
      scale(1);
  }
}

@media (max-width: 640px) {
  .paw-container {
    width: 88px;
    height: 88px;
    right: -14px;
  }

  .paw-svg {
    width: 88px;
    height: 88px;
  }

  .paw-spark {
    transform: scale(0.8);
  }
}

@media (max-width: 360px) {
  .paw-container {
    width: 78px;
    height: 78px;
  }

  .paw-svg {
    width: 78px;
    height: 78px;
  }
}
</style>
