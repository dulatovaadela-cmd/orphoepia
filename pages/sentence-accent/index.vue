<template>
  <div
    class="relative min-h-screen bg-gradient-to-br from-blue-100 via-purple-50 to-pink-100 py-6 px-3 sm:py-8 sm:px-4"
  >

    <!-- ОКОШКО НУМЕРАЦИИ -->
    <div
      v-if="!finished"
      class="mx-auto mt-4 w-full max-w-2xl rounded-xl border border-purple-200 bg-white p-3 shadow-xl sm:p-4 md:absolute md:right-4 md:top-4 md:mt-0 md:w-72"
    >
      <div class="grid grid-cols-5 gap-2 sm:gap-3">
        <button
          v-for="(q, index) in questions"
          :key="index"
          @click="jumpTo(index)"
          class="flex h-9 w-full items-center justify-center rounded-lg border text-sm font-semibold transition sm:h-10 sm:text-base"
          :class="[
            currentIdx === index
              ? 'border-blue-500 bg-blue-200 text-blue-800'
              : answeredQuestions[index]
                ? 'border-green-500 bg-green-200 text-green-800'
                : 'border-gray-300 bg-white text-gray-700 hover:bg-gray-100'
          ]"
        >
          {{ index + 1 }}
        </button>
      </div>
    </div>

    <!-- ОСНОВНОЙ БЛОК -->
    <div
      class="mx-auto w-full max-w-2xl rounded-2xl border-t-4 border-purple-400 bg-white p-4 shadow-2xl sm:p-6 md:p-8"
    >

      <h2
        class="mb-2 text-center text-2xl font-bold text-purple-700 sm:text-3xl"
      >
        🗣️ Укажи верное ударение
      </h2>

      <p
        class="mb-5 text-center text-sm text-gray-500 sm:mb-6 sm:text-base"
      >
        Прочитайте предложение и укажите, верно ли поставлено ударение.
      </p>

      <!-- ЗАДАНИЕ -->
      <div v-if="!finished || showPaw">

        <div
          class="mb-4 text-center text-base font-semibold text-blue-700 sm:text-lg"
        >
          Вопрос {{ currentIdx + 1 }} из {{ questions.length }}
        </div>

        <!-- ПРЕДЛОЖЕНИЕ -->
        <div
          class="mb-6 rounded-xl bg-purple-50 px-3 py-4 text-center sm:px-5 sm:py-5"
        >
          <p
            class="break-words text-base leading-relaxed text-gray-800 sm:text-xl"
          >
            <span
              v-html="currentQuestion.sentence"
              class="font-medium"
            ></span>
          </p>
        </div>

        <!-- ВАРИАНТЫ -->
        <div class="mb-6 flex flex-col gap-3 sm:gap-4">

          <button
            v-for="(option, idx) in ['Верно', 'Неверно']"
            :key="idx"
            :disabled="showResult"
            @click="selectOption(idx)"
            class="relative w-full min-w-0 min-h-[54px] overflow-hidden rounded-xl border py-3 pl-4 pr-16 text-left text-base font-medium transition sm:text-lg"
            :class="[
              selectedIdx === idx
                ? 'border-blue-400 bg-blue-100'
                : 'border-gray-300 bg-white',

              showResult && idx === currentQuestion.correct
                ? 'border-green-500 bg-green-100 shadow-md'
                : '',

              showResult &&
              selectedIdx === idx &&
              selectedIdx !== currentQuestion.correct
                ? 'border-red-500 bg-red-100 shadow-md'
                : ''
            ]"
          >

            <span
              class="block break-words whitespace-normal leading-relaxed"
            >
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

                    <radialGradient
                      id="furGradientSentence"
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
                      id="padGradientSentence"
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
                      id="pawShadowSentence"
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

                  <!-- ПАЛЬЧИКИ -->
                  <g
                    fill="url(#furGradientSentence)"
                    stroke="#8b572f"
                    stroke-width="1.2"
                    filter="url(#pawShadowSentence)"
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

                    <!-- ЛАДОНЬ -->
                    <ellipse
                      cx="47"
                      cy="54"
                      rx="25"
                      ry="28"
                    />

                  </g>

                  <!-- РОЗОВЫЕ ПОДУШЕЧКИ -->
                  <g
                    fill="url(#padGradientSentence)"
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

                  <!-- ПЯТНЫШКИ ЛЕОПАРДА -->
                  <g
                    fill="#7b4a2c"
                    opacity="0.9"
                  >

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

                  <!-- МЯГКИЙ БЛИК -->
                  <ellipse
                    cx="39"
                    cy="48"
                    rx="6"
                    ry="9"
                    fill="#ffffff"
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
          class="flex flex-col gap-4 text-center"
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
              ❌ Неверно. Правильно:
              <b>
                {{ currentQuestion.correctAnswer }}
              </b>
            </p>

            <!-- АУДИО -->
            <audio
              v-if="currentQuestion.audio"
              ref="audioRef"
              :src="currentQuestion.audio"
              controls
              class="mx-auto my-2 max-w-full"
            ></audio>

          </div>

          <!-- НАВИГАЦИЯ -->
          <div
            class="mt-2 flex flex-col gap-3 sm:flex-row sm:justify-between sm:gap-4"
          >

            <button
              @click="prevQuestion"
              :disabled="currentIdx === 0"
              class="w-full rounded-lg bg-gray-300 px-4 py-3 font-bold text-gray-800 transition hover:bg-gray-400 disabled:opacity-50 sm:flex-1"
            >
              ⬅️ Назад
            </button>

            <button
              @click="nextQuestion"
              :disabled="currentIdx >= questions.length - 1"
              class="w-full rounded-lg bg-purple-600 px-4 py-3 font-bold text-white transition hover:bg-purple-700 disabled:opacity-50 sm:flex-1"
            >
              Вперёд ➡️
            </button>

          </div>

        </div>

      </div>

      <!-- ФИНАЛЬНЫЙ РЕЗУЛЬТАТ -->
      <div
        v-else
        class="mt-6 text-center"
      >

        <div
          class="rounded-2xl bg-purple-50 p-4 shadow-lg sm:p-8"
        >

          <p
            class="mb-3 text-2xl font-extrabold text-purple-700 sm:text-3xl"
          >
            🎉 Ваши результаты
          </p>

          <p
            class="mb-5 break-words text-base text-gray-600 sm:text-lg"
          >
            Вы выполнили задание
            «Укажи верное ударение»
          </p>

          <div
            class="mb-5 rounded-xl bg-white p-5 shadow-md sm:p-6"
          >

            <p class="mb-2 text-gray-600">
              Результат
            </p>

            <p
              class="mb-3 text-4xl font-extrabold text-green-600"
            >
              {{ correctCount }}/{{ questions.length }}
            </p>

            <p
              class="mb-3 text-2xl font-bold text-blue-600"
            >
              {{ percent }}%
            </p>

            <p class="text-xl font-bold">
              Оценка:
              <span
                :class="gradeColor"
              >
                {{ grade }}
              </span>
            </p>

          </div>

          <!-- СОХРАНЕНИЕ -->
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

          <!-- КНОПКИ -->
          <div class="flex flex-col gap-3">

            <NuxtLink
              to="/results"
              class="block rounded-xl bg-purple-600 px-5 py-3 font-bold text-white shadow-md transition hover:bg-purple-700"
            >
              📊 Мой прогресс
            </NuxtLink>

            <NuxtLink
              to="/"
              class="mt-1 block text-blue-600 underline hover:text-blue-800"
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
import {
  ref,
  computed,
  nextTick,
  watch,
  onMounted,
  onBeforeUnmount
} from 'vue'

interface Question {
  sentence: string
  correct: number
  correctAnswer: string
  audio?: string
}

interface Student {
  name: string
  class: string
}

const supabase = useSupabaseClient()

const questions: Question[] = [
  {
    sentence: 'Учитель сделал ему строгие <b>вЫговоры</b> за небрежное отношение к учёбе.',
    correct: 0,
    correctAnswer: 'верно',
    audio: '/audio/vygovory.ogg'
  },
  {
    sentence: 'Солнце падало <b>нАискось</b>, освещая пыльную дорогу.',
    correct: 0,
    correctAnswer: 'нАискось',
    audio: '/audio/naiskos.ogg'
  },
  {
    sentence: 'После университета он поступил на факультет <b>агрономИи</b>.',
    correct: 1,
    correctAnswer: 'агронОмии',
    audio: '/audio/agronomii.ogg'
  },
  {
    sentence: 'Судьба свела её с настоящим <b>избрАнником</b> сердца.',
    correct: 0,
    correctAnswer: 'верно',
    audio: '/audio/izbrannik.ogg'
  },
  {
    sentence: 'Этот обычай существует <b>издавнА</b> и передаётся из поколения в поколение.',
    correct: 1,
    correctAnswer: 'ИздавнА',
    audio: '/audio/izdavna.ogg'
  },
  {
    sentence: '<b>АнатОм</b> внимательно рассматривал строение тела.',
    correct: 1,
    correctAnswer: 'анАтом',
    audio: '/audio/anatom.ogg'
  },
  {
    sentence: 'Он ударил <b>наОтмашь</b>, не рассчитав силы.',
    correct: 0,
    correctAnswer: 'верно',
    audio: '/audio/naotmash.ogg'
  },
  {
    sentence: 'Ребёнок осторожно <b>черпАет</b> воду из ведра.',
    correct: 1,
    correctAnswer: 'чЕрпает',
    audio: '/audio/cherpaet.ogg'
  },
  {
    sentence: 'Учитель говорил об <b>обеспечЕнии</b> безопасности.',
    correct: 1,
    correctAnswer: 'обеспЕчении',
    audio: '/audio/obespechenie.ogg'
  },
  {
    sentence: 'На платье были красивые <b>кружевА</b> ручной работы.',
    correct: 0,
    correctAnswer: 'верно',
    audio: '/audio/kruzheva.ogg'
  },
  {
    sentence: 'В доме отремонтировали <b>мусоропровОд</b>.',
    correct: 0,
    correctAnswer: 'верно',
    audio: '/audio/musoroprovod.ogg'
  },
  {
    sentence: 'Звук бормашины <b>свЕрлит</b> ухо до боли.',
    correct: 1,
    correctAnswer: 'сверлИт',
    audio: '/audio/sverl.ogg'
  },
  {
    sentence: 'Зуб сильно болел, и врач начал его <b>пломбирОвать</b>.',
    correct: 1,
    correctAnswer: 'пломбировАть',
    audio: '/audio/plombirovat.ogg'
  },
  {
    sentence: 'В его глазах сверкнула <b>Искра</b> вдохновения.',
    correct: 0,
    correctAnswer: 'верно',
    audio: '/audio/iskra.ogg'
  },
  {
    sentence: 'Учителя решили <b>премИровать</b> лучших учеников.',
    correct: 1,
    correctAnswer: 'премировАть',
    audio: '/audio/premirovat.ogg'
  }
]

const currentIdx = ref(0)

const selectedIdx = ref<number | null>(null)

const showResult = ref(false)

const correctCount = ref(0)

const userAnswers = ref<(number | null)[]>(
  Array(questions.length).fill(null)
)

const answeredQuestions = ref<boolean[]>(
  Array(questions.length).fill(false)
)

const showPaw = ref(false)

let pawTimer: ReturnType<typeof setTimeout> | null = null

const audioRef = ref<HTMLAudioElement | null>(null)

const studentName = ref('')

const studentClass = ref('')

const attemptId = ref<string | number | null>(null)

const startedAt = ref<string>('')

const saveStatus = ref<'saving' | 'saved' | 'error'>('saving')

const currentQuestion = computed(
  () => questions[currentIdx.value]!
)

const finished = computed(() =>
  answeredQuestions.value.every(a => a)
)

const percent = computed(() =>
  Math.round(
    (correctCount.value / questions.length) * 100
  )
)

const grade = computed(() => {
  if (percent.value < 50) return 2
  if (percent.value <= 70) return 3
  if (percent.value <= 84) return 4
  return 5
})

const gradeColor = computed(() => {
  return grade.value === 5
    ? 'text-green-600'
    : grade.value === 4
      ? 'text-blue-600'
      : grade.value === 3
        ? 'text-yellow-600'
        : 'text-red-600'
})

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

    studentName.value = student.name.trim()

    studentClass.value = student.class.trim()

    startedAt.value =
      new Date().toISOString()

    const { data, error } = await supabase
      .from('attempts')
      .insert({
        student_name: studentName.value,
        class: studentClass.value,
        task_name: 'Укажи верное ударение',
        total_questions: questions.length,
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

  answeredQuestions.value[currentIdx.value] = true

  if (
    idx === currentQuestion.value.correct
  ) {
    correctCount.value++

    triggerCorrectPaw()
  }
}

async function saveResult() {
  if (!attemptId.value) {
    saveStatus.value = 'error'
    return
  }

  saveStatus.value = 'saving'

  const completedAt =
    new Date().toISOString()

  const { error } = await supabase
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

watch(
  showResult,
  async (value) => {
    if (
      value &&
      currentQuestion.value.audio
    ) {
      await nextTick()

      audioRef.value
        ?.play()
        .catch(() => {})
    }
  }
)

watch(
  finished,
  async (isFinished) => {
    if (isFinished) {
      await saveResult()
    }
  }
)

function nextQuestion() {
  showPaw.value = false

  if (
    currentIdx.value <
    questions.length - 1
  ) {
    currentIdx.value++
  }

  selectedIdx.value =
    userAnswers.value[currentIdx.value]

  showResult.value =
    answeredQuestions.value[currentIdx.value]
}

function prevQuestion() {
  showPaw.value = false

  if (currentIdx.value > 0) {
    currentIdx.value--
  }

  selectedIdx.value =
    userAnswers.value[currentIdx.value]

  showResult.value =
    answeredQuestions.value[currentIdx.value]
}

function jumpTo(index: number) {
  showPaw.value = false

  currentIdx.value = index

  selectedIdx.value =
    userAnswers.value[index]

  showResult.value =
    answeredQuestions.value[index]
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

/* ЖИВОЕ ДВИЖЕНИЕ ЛАПКИ */

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

/* ИСКОРКИ */

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

/* ОЧЕНЬ МАЛЕНЬКИЕ ЭКРАНЫ */

@media (max-width: 360px) {
  .paw-svg {
    width: 44px;
    height: 44px;
  }
}
</style>
