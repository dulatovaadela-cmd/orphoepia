```vue
<template>
  <div
    class="relative min-h-screen overflow-x-hidden bg-gradient-to-br from-amber-50 via-orange-50 to-rose-50 py-6 px-3 sm:py-8 sm:px-4"
  >

    <!-- ========================================================= -->
    <!-- ОКОШКО НУМЕРАЦИИ -->
    <!-- ========================================================= -->

    <div
      v-if="!finished"
      class="mx-auto mt-4 w-full max-w-2xl rounded-2xl border-2 border-amber-300 bg-[#f7ead2] p-3 shadow-xl sm:p-4 md:absolute md:right-5 md:top-5 md:mt-0 md:w-72"
    >
      <div
        class="mb-3 text-center text-sm font-extrabold uppercase tracking-wide text-amber-800"
      >
        Вопросы
      </div>

      <div class="grid grid-cols-5 gap-2 sm:gap-3">
        <button
          v-for="(q, index) in questions"
          :key="index"
          @click="jumpTo(index)"
          class="number-button flex h-9 w-full items-center justify-center rounded-lg border-2 text-sm font-extrabold transition sm:h-10 sm:text-base"
          :class="[
            currentIdx === index
              ? 'current-number'
              : answeredQuestions[index]
                ? 'answered-number'
                : 'empty-number'
          ]"
        >
          {{ index + 1 }}
        </button>
      </div>

      <div class="mt-3 flex justify-center gap-3 text-[10px] font-semibold text-gray-600 sm:text-xs">
        <span class="flex items-center gap-1">
          <span class="legend-dot bg-blue-400"></span>
          текущий
        </span>

        <span class="flex items-center gap-1">
          <span class="legend-dot bg-green-500"></span>
          выполнен
        </span>

        <span class="flex items-center gap-1">
          <span class="legend-dot bg-white border border-gray-300"></span>
          новый
        </span>
      </div>
    </div>

    <!-- ========================================================= -->
    <!-- ОСНОВНОЙ БЛОК -->
    <!-- ========================================================= -->

    <div
      class="mx-auto w-full max-w-2xl rounded-[28px] border-2 border-amber-200 bg-white p-4 shadow-2xl sm:p-6 md:p-8"
    >

      <!-- ЗАГОЛОВОК -->

      <div class="mb-6 text-center">

        <div
          class="mb-2 text-4xl drop-shadow-sm sm:text-5xl"
        >
          🗣️
        </div>

        <h2
          class="text-2xl font-black text-amber-800 sm:text-3xl"
        >
          Укажи верное ударение
        </h2>

        <p
          class="mx-auto mt-2 max-w-lg text-sm leading-relaxed text-gray-500 sm:text-base"
        >
          Прочитайте предложение и укажите,
          верно ли поставлено ударение.
        </p>

      </div>

      <!-- ======================================================= -->
      <!-- ЗАДАНИЕ -->
      <!-- ======================================================= -->

      <div v-if="!finished || showPaw">

        <!-- НОМЕР ВОПРОСА -->

        <div
          class="mb-4 flex items-center justify-center"
        >
          <div
            class="rounded-full bg-[#f7ead2] px-5 py-2 text-sm font-extrabold text-amber-800 shadow-sm sm:text-base"
          >
            Вопрос {{ currentIdx + 1 }} из {{ questions.length }}
          </div>
        </div>

        <!-- ===================================================== -->
        <!-- ПРЕДЛОЖЕНИЕ -->
        <!-- ===================================================== -->

        <div
          class="question-card mb-6 rounded-2xl border-2 border-amber-100 bg-gradient-to-br from-amber-50 to-orange-50 px-4 py-5 shadow-inner sm:px-6 sm:py-6"
        >
          <p
            class="break-words text-center text-base leading-relaxed text-gray-800 sm:text-xl"
          >
            <span
              v-html="currentQuestion.sentence"
              class="font-semibold"
            ></span>
          </p>
        </div>

        <!-- ===================================================== -->
        <!-- ВАРИАНТЫ ОТВЕТА -->
        <!-- ===================================================== -->

        <div class="mb-6 flex flex-col gap-4">

          <button
            v-for="(option, idx) in ['Верно', 'Неверно']"
            :key="idx"
            :disabled="showResult"
            @click="selectOption(idx)"
            class="answer-button relative w-full min-h-[64px] min-w-0 overflow-visible rounded-2xl border-2 py-3 pl-5 pr-20 text-left text-base font-extrabold sm:min-h-[68px] sm:text-lg"
            :class="[
              !showResult && selectedIdx === idx
                ? 'selected-answer'
                : '',

              !showResult && selectedIdx !== idx
                ? 'normal-answer'
                : '',

              showResult &&
              idx === currentQuestion.correct
                ? 'correct-answer'
                : '',

              showResult &&
              selectedIdx === idx &&
              selectedIdx !== currentQuestion.correct
                ? 'wrong-answer'
                : ''
            ]"
          >

            <span
              class="relative z-10 block break-words whitespace-normal leading-relaxed"
            >
              {{ option }}
            </span>

            <!-- ================================================= -->
            <!-- ЛЕОПАРДОВАЯ ЛАПКА -->
            <!-- ================================================= -->

            <Transition name="paw">

              <div
                v-if="
                  showPaw &&
                  idx === currentQuestion.correct &&
                  selectedIdx === currentQuestion.correct
                "
                class="paw-wrapper pointer-events-none absolute right-0 top-1/2 z-30 -translate-y-1/2"
                aria-hidden="true"
              >

                <!-- ДВИЖЕНИЕ К ОТВЕТУ -->

                <div class="paw-push">

                  <svg
                    viewBox="0 0 100 100"
                    class="paw-svg"
                    xmlns="http://www.w3.org/2000/svg"
                  >

                    <defs>

                      <!-- Мех -->

                      <radialGradient
                        id="furGradientSentence"
                        cx="30%"
                        cy="20%"
                        r="85%"
                      >
                        <stop
                          offset="0%"
                          stop-color="#fff0c7"
                        />

                        <stop
                          offset="35%"
                          stop-color="#efc27f"
                        />

                        <stop
                          offset="70%"
                          stop-color="#d8924c"
                        />

                        <stop
                          offset="100%"
                          stop-color="#9d572c"
                        />
                      </radialGradient>

                      <!-- Подушечки -->

                      <radialGradient
                        id="padGradientSentence"
                        cx="35%"
                        cy="25%"
                        r="80%"
                      >
                        <stop
                          offset="0%"
                          stop-color="#fff1f2"
                        />

                        <stop
                          offset="45%"
                          stop-color="#f7aebd"
                        />

                        <stop
                          offset="100%"
                          stop-color="#c9687d"
                        />
                      </radialGradient>

                      <!-- Свечение -->

                      <filter
                        id="pawGlowSentence"
                        x="-60%"
                        y="-60%"
                        width="220%"
                        height="220%"
                      >
                        <feDropShadow
                          dx="0"
                          dy="4"
                          stdDeviation="3"
                          flood-color="#8b4b22"
                          flood-opacity="0.4"
                        />
                      </filter>

                    </defs>

                    <!-- ПАЛЬЧИКИ -->

                    <g
                      fill="url(#furGradientSentence)"
                      stroke="#80451f"
                      stroke-width="1.5"
                      filter="url(#pawGlowSentence)"
                    >

                      <ellipse
                        cx="21"
                        cy="26"
                        rx="10"
                        ry="15"
                        transform="rotate(-27 21 26)"
                      />

                      <ellipse
                        cx="40"
                        cy="18"
                        rx="10"
                        ry="15"
                        transform="rotate(-8 40 18)"
                      />

                      <ellipse
                        cx="60"
                        cy="20"
                        rx="10"
                        ry="15"
                        transform="rotate(12 60 20)"
                      />

                      <ellipse
                        cx="77"
                        cy="31"
                        rx="9"
                        ry="14"
                        transform="rotate(28 77 31)"
                      />

                      <!-- ЛАДОНЬ -->

                      <ellipse
                        cx="49"
                        cy="58"
                        rx="28"
                        ry="30"
                      />

                    </g>

                    <!-- РОЗОВЫЕ ПОДУШЕЧКИ -->

                    <g
                      fill="url(#padGradientSentence)"
                      stroke="#b85d73"
                      stroke-width="1.2"
                    >

                      <ellipse
                        cx="21"
                        cy="26"
                        rx="5.5"
                        ry="8"
                      />

                      <ellipse
                        cx="40"
                        cy="18"
                        rx="5.5"
                        ry="8"
                      />

                      <ellipse
                        cx="60"
                        cy="20"
                        rx="5.5"
                        ry="8"
                      />

                      <ellipse
                        cx="77"
                        cy="31"
                        rx="5"
                        ry="7"
                      />

                      <ellipse
                        cx="49"
                        cy="58"
                        rx="15"
                        ry="18"
                      />

                    </g>

                    <!-- ЛЕОПАРДОВЫЕ ПЯТНА -->

                    <g
                      fill="#713d24"
                      opacity="0.95"
                    >

                      <circle cx="13" cy="17" r="2.5" />
                      <circle cx="28" cy="13" r="2.8" />
                      <circle cx="35" cy="31" r="2.4" />
                      <circle cx="49" cy="9" r="2.6" />
                      <circle cx="65" cy="14" r="2.8" />
                      <circle cx="82" cy="25" r="2.5" />

                      <circle cx="16" cy="46" r="2.8" />
                      <circle cx="31" cy="52" r="2.3" />
                      <circle cx="66" cy="45" r="2.7" />
                      <circle cx="78" cy="55" r="2.5" />

                      <circle cx="35" cy="76" r="2.7" />
                      <circle cx="58" cy="78" r="2.9" />
                      <circle cx="68" cy="67" r="2.3" />

                    </g>

                    <!-- БЛИК -->

                    <ellipse
                      cx="40"
                      cy="48"
                      rx="7"
                      ry="10"
                      fill="white"
                      opacity="0.25"
                    />

                  </svg>

                  <!-- ИСКОРКИ -->

                  <span class="paw-spark spark-one">
                    ✦
                  </span>

                  <span class="paw-spark spark-two">
                    ✦
                  </span>

                  <span class="paw-spark spark-three">
                    •
                  </span>

                  <span class="paw-spark spark-four">
                    ✦
                  </span>

                </div>

              </div>

            </Transition>

          </button>

        </div>

        <!-- ===================================================== -->
        <!-- РЕЗУЛЬТАТ -->
        <!-- ===================================================== -->

        <div
          v-if="showResult"
          class="flex flex-col gap-4 text-center"
        >

          <div
            class="result-message rounded-2xl px-4 py-4"
            :class="
              selectedIdx === currentQuestion.correct
                ? 'correct-message'
                : 'wrong-message'
            "
          >

            <p
              v-if="
                selectedIdx === currentQuestion.correct
              "
              class="font-extrabold text-green-700"
            >
              ✅ Правильно! Отлично!
            </p>

            <p
              v-else
              class="font-extrabold text-red-700"
            >
              ❌ Неверно.
              Правильно:
              <b>
                {{ currentQuestion.correctAnswer }}
              </b>
            </p>

          </div>

          <!-- АУДИО -->

          <audio
            v-if="currentQuestion.audio"
            ref="audioRef"
            :src="currentQuestion.audio"
            controls
            class="mx-auto my-1 max-w-full"
          ></audio>

          <!-- ================================================= -->
          <!-- НАВИГАЦИЯ -->
          <!-- ================================================= -->

          <div
            class="mt-2 flex flex-col gap-3 sm:flex-row sm:justify-between sm:gap-4"
          >

            <button
              @click="prevQuestion"
              :disabled="currentIdx === 0"
              class="nav-button w-full rounded-xl bg-gray-200 px-4 py-3 font-extrabold text-gray-800 shadow-sm transition hover:bg-gray-300 disabled:cursor-not-allowed disabled:opacity-40 sm:flex-1"
            >
              ⬅️ Назад
            </button>

            <button
              @click="nextQuestion"
              :disabled="
                currentIdx >= questions.length - 1
              "
              class="nav-button w-full rounded-xl bg-amber-600 px-4 py-3 font-extrabold text-white shadow-md transition hover:bg-amber-700 disabled:cursor-not-allowed disabled:opacity-40 sm:flex-1"
            >
              Вперёд ➡️
            </button>

          </div>

        </div>

      </div>

      <!-- ======================================================= -->
      <!-- ФИНАЛ -->
      <!-- ======================================================= -->

      <div
        v-else
        class="mt-4 text-center"
      >

        <div
          class="rounded-3xl border-2 border-amber-200 bg-gradient-to-br from-amber-50 to-orange-50 p-4 shadow-xl sm:p-8"
        >

          <div class="mb-3 text-5xl">
            🎉
          </div>

          <p
            class="mb-3 text-2xl font-black text-amber-800 sm:text-3xl"
          >
            Ваши результаты
          </p>

          <p
            class="mb-5 break-words text-base text-gray-600 sm:text-lg"
          >
            Вы выполнили задание
            «Укажи верное ударение»
          </p>

          <div
            class="mb-5 rounded-2xl bg-white p-5 shadow-md sm:p-6"
          >

            <p class="mb-2 font-semibold text-gray-500">
              Результат
            </p>

            <p
              class="mb-3 text-4xl font-black text-green-600"
            >
              {{ correctCount }}/{{ questions.length }}
            </p>

            <p
              class="mb-3 text-2xl font-black text-amber-600"
            >
              {{ percent }}%
            </p>

            <p class="text-xl font-bold">
              Оценка:
              <span :class="gradeColor">
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
            class="mb-4 font-bold text-green-600"
          >
            ✅ Результат сохранён
          </p>

          <p
            v-else-if="saveStatus === 'error'"
            class="mb-4 font-bold text-red-600"
          >
            ⚠️ Не удалось сохранить результат
          </p>

          <!-- КНОПКИ -->

          <div class="flex flex-col gap-3">

            <NuxtLink
              to="/results"
              class="block rounded-xl bg-amber-600 px-5 py-3 font-extrabold text-white shadow-md transition hover:bg-amber-700"
            >
              📊 Мой прогресс
            </NuxtLink>

            <NuxtLink
              to="/"
              class="mt-1 block text-amber-700 underline hover:text-amber-900"
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
    sentence:
      'Учитель сделал ему строгие <b>вЫговоры</b> за небрежное отношение к учёбе.',
    correct: 0,
    correctAnswer: 'верно',
    audio: '/audio/vygovory.ogg'
  },

  {
    sentence:
      'Солнце падало <b>нАискось</b>, освещая пыльную дорогу.',
    correct: 0,
    correctAnswer: 'нАискось',
    audio: '/audio/naiskos.ogg'
  },

  {
    sentence:
      'После университета он поступил на факультет <b>агрономИи</b>.',
    correct: 1,
    correctAnswer: 'агронОмии',
    audio: '/audio/agronomii.ogg'
  },

  {
    sentence:
      'Судьба свела её с настоящим <b>избрАнником</b> сердца.',
    correct: 0,
    correctAnswer: 'верно',
    audio: '/audio/izbrannik.ogg'
  },

  {
    sentence:
      'Этот обычай существует <b>издавнА</b> и передаётся из поколения в поколение.',
    correct: 1,
    correctAnswer: 'ИздавнА',
    audio: '/audio/izdavna.ogg'
  },

  {
    sentence:
      '<b>АнатОм</b> внимательно рассматривал строение тела.',
    correct: 1,
    correctAnswer: 'анАтом',
    audio: '/audio/anatom.ogg'
  },

  {
    sentence:
      'Он ударил <b>наОтмашь</b>, не рассчитав силы.',
    correct: 0,
    correctAnswer: 'верно',
    audio: '/audio/naotmash.ogg'
  },

  {
    sentence:
      'Ребёнок осторожно <b>черпАет</b> воду из ведра.',
    correct: 1,
    correctAnswer: 'чЕрпает',
    audio: '/audio/cherpaet.ogg'
  },

  {
    sentence:
      'Учитель говорил об <b>обеспечЕнии</b> безопасности.',
    correct: 1,
    correctAnswer: 'обеспЕчении',
    audio: '/audio/obespechenie.ogg'
  },

  {
    sentence:
      'На платье были красивые <b>кружевА</b> ручной работы.',
    correct: 0,
    correctAnswer: 'верно',
    audio: '/audio/kruzheva.ogg'
  },

  {
    sentence:
      'В доме отремонтировали <b>мусоропровОд</b>.',
    correct: 0,
    correctAnswer: 'верно',
    audio: '/audio/musoroprovod.ogg'
  },

  {
    sentence:
      'Звук бормашины <b>свЕрлит</b> ухо до боли.',
    correct: 1,
    correctAnswer: 'сверлИт',
    audio: '/audio/sverl.ogg'
  },

  {
    sentence:
      'Зуб сильно болел, и врач начал его <b>пломбирОвать</b>.',
    correct: 1,
    correctAnswer: 'пломбировАть',
    audio: '/audio/plombirovat.ogg'
  },

  {
    sentence:
      'В его глазах сверкнула <b>Искра</b> вдохновения.',
    correct: 0,
    correctAnswer: 'верно',
    audio: '/audio/iskra.ogg'
  },

  {
    sentence:
      'Учителя решили <b>премИровать</b> лучших учеников.',
    correct: 1,
    correctAnswer: 'премировАть',
    audio: '/audio/premirovat.ogg'
  }

]


const currentIdx = ref(0)

const selectedIdx =
  ref<number | null>(null)

const showResult = ref(false)

const correctCount = ref(0)

const userAnswers =
  ref<(number | null)[]>(
    Array(questions.length).fill(null)
  )

const answeredQuestions =
  ref<boolean[]>(
    Array(questions.length).fill(false)
  )

const showPaw = ref(false)

let pawTimer:
  ReturnType<typeof setTimeout> | null = null

const audioRef =
  ref<HTMLAudioElement | null>(null)

const studentName = ref('')

const studentClass = ref('')

const attemptId =
  ref<string | number | null>(null)

const startedAt = ref<string>('')

const saveStatus =
  ref<'saving' | 'saved' | 'error'>('saving')


const currentQuestion =
  computed(
    () => questions[currentIdx.value]!
  )


const finished =
  computed(() =>
    answeredQuestions.value.every(
      a => a
    )
  )


const percent =
  computed(() =>
    Math.round(
      (correctCount.value /
        questions.length) *
        100
    )
  )


const grade =
  computed(() => {

    if (percent.value < 50)
      return 2

    if (percent.value <= 70)
      return 3

    if (percent.value <= 84)
      return 4

    return 5

  })


const gradeColor =
  computed(() => {

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
    localStorage.getItem(
      'orphoepia_student'
    )


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

          student_name:
            studentName.value,

          class:
            studentClass.value,

          task_name:
            'Укажи верное ударение',

          total_questions:
            questions.length,

          started_at:
            startedAt.value

        })
        .select('id')
        .single()


    if (error) {

      console.error(
        'Ошибка создания попытки:',
        error
      )

      saveStatus.value =
        'error'

      return

    }


    attemptId.value =
      data.id

    saveStatus.value =
      'saving'

  }

  catch (error) {

    console.error(
      'Ошибка чтения данных ученика:',
      error
    )

    await navigateTo('/')

  }

})


/* ========================================================= */
/* ЛАПКА */
/* ========================================================= */

function triggerCorrectPaw() {

  if (pawTimer) {

    clearTimeout(pawTimer)

  }


  showPaw.value = false


  requestAnimationFrame(() => {

    showPaw.value = true

  })


  pawTimer =
    setTimeout(() => {

      showPaw.value = false

      pawTimer = null

    }, 3600)

}


/* ========================================================= */
/* ВЫБОР ОТВЕТА */
/* ========================================================= */

function selectOption(idx: number) {

  if (showResult.value)
    return


  selectedIdx.value = idx


  userAnswers.value[
    currentIdx.value
  ] = idx


  showResult.value = true


  answeredQuestions.value[
    currentIdx.value
  ] = true


  if (
    idx ===
    currentQuestion.value.correct
  ) {

    correctCount.value++

    triggerCorrectPaw()

  }

}


/* ========================================================= */
/* СОХРАНЕНИЕ */
/* ========================================================= */

async function saveResult() {

  if (!attemptId.value) {

    saveStatus.value =
      'error'

    return

  }


  saveStatus.value =
    'saving'


  const completedAt =
    new Date().toISOString()


  const { error } =
    await supabase
      .from('attempts')
      .update({

        score:
          correctCount.value,

        percentage:
          percent.value,

        completed_at:
          completedAt

      })
      .eq(
        'id',
        attemptId.value
      )


  if (error) {

    console.error(
      'Ошибка сохранения результата:',
      error
    )

    saveStatus.value =
      'error'

    return

  }


  saveStatus.value =
    'saved'

}


/* ========================================================= */
/* АВТОЗАПУСК АУДИО */
/* ========================================================= */

watch(
  showResult,
  async value => {

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


/* ========================================================= */
/* ЗАВЕРШЕНИЕ */
/* ========================================================= */

watch(
  finished,
  async isFinished => {

    if (isFinished) {

      await saveResult()

    }

  }
)


/* ========================================================= */
/* НАВИГАЦИЯ */
/* ========================================================= */

function nextQuestion() {

  showPaw.value = false


  if (
    currentIdx.value <
    questions.length - 1
  ) {

    currentIdx.value++

  }


  selectedIdx.value =
    userAnswers.value[
      currentIdx.value
    ]


  showResult.value =
    answeredQuestions.value[
      currentIdx.value
    ]

}


function prevQuestion() {

  showPaw.value = false


  if (
    currentIdx.value > 0
  ) {

    currentIdx.value--

  }


  selectedIdx.value =
    userAnswers.value[
      currentIdx.value
    ]


  showResult.value =
    answeredQuestions.value[
      currentIdx.value
    ]

}


function jumpTo(index: number) {

  showPaw.value = false


  currentIdx.value = index


  selectedIdx.value =
    userAnswers.value[index]


  showResult.value =
    answeredQuestions.value[index]

}


/* ========================================================= */
/* ОЧИСТКА */
/* ========================================================= */

onBeforeUnmount(() => {

  if (pawTimer) {

    clearTimeout(pawTimer)

  }

})

</script>


<style scoped>

/* ========================================================= */
/* ОБЩИЕ АНИМАЦИИ */
/* ========================================================= */

button {
  transition:
    transform 0.2s ease,
    box-shadow 0.2s ease,
    background-color 0.2s ease,
    border-color 0.2s ease;
}


button:hover:not(:disabled) {
  transform: translateY(-2px);
}


/* ========================================================= */
/* НУМЕРАЦИЯ */
/* ========================================================= */

.number-button {
  box-shadow:
    0 2px 5px rgba(120, 75, 30, 0.08);
}


.number-button:hover:not(:disabled) {
  transform: scale(1.07);
}


.current-number {
  border-color: #60a5fa;
  background: #bfdbfe;
  color: #1e40af;
  box-shadow:
    0 0 0 3px rgba(96, 165, 250, 0.18);
}


.answered-number {
  border-color: #22c55e;
  background: #bbf7d0;
  color: #166534;
}


.empty-number {
  border-color: #d6d3d1;
  background: #fff;
  color: #57534e;
}


.empty-number:hover {
  background: #fef3c7;
  border-color: #f59e0b;
}


/* ========================================================= */
/* ТОЧКИ ЛЕГЕНДЫ */
/* ========================================================= */

.legend-dot {
  display: inline-block;
  width: 8px;
  height: 8px;
  border-radius: 999px;
}


/* ========================================================= */
/* КАРТОЧКА ВОПРОСА */
/* ========================================================= */

.question-card {
  box-shadow:
    inset 0 1px 3px rgba(120, 75, 30, 0.05),
    0 4px 12px rgba(120, 75, 30, 0.06);
}


.question-card :deep(b) {
  color: #92400e;
  font-weight: 900;
}


/* ========================================================= */
/* ОТВЕТЫ */
/* ========================================================= */

.answer-button {
  box-shadow:
    0 4px 10px rgba(0, 0, 0, 0.06);
}


.normal-answer {
  border-color: #d6d3d1;
  background: #fff;
  color: #292524;
}


.normal-answer:hover {
  border-color: #f59e0b;
  background: #fffbeb;
  box-shadow:
    0 7px 16px rgba(245, 158, 11, 0.15);
}


.selected-answer {
  border-color: #60a5fa;
  background: #dbeafe;
  color: #1e3a8a;
}


/* ========================================================= */
/* ПРАВИЛЬНЫЙ ОТВЕТ — ЯРКО-ЗЕЛЁНЫЙ */
/* ========================================================= */

.correct-answer {
  border-color: #16a34a !important;
  background:
    linear-gradient(
      135deg,
      #dcfce7,
      #bbf7d0
    ) !important;
  color: #14532d !important;

  box-shadow:
    0 0 0 4px rgba(34, 197, 94, 0.14),
    0 10px 25px rgba(34, 197, 94, 0.22);

  animation:
    correct-pulse 0.65s ease-out;
}


.correct-answer::after {
  content: '✓';

  position: absolute;

  right: 66px;
  top: 50%;

  transform:
    translateY(-50%);

  font-size: 24px;
  font-weight: 900;

  color: #16a34a;

  opacity: 0.9;
}


/* ========================================================= */
/* НЕПРАВИЛЬНЫЙ ОТВЕТ */
/* ========================================================= */

.wrong-answer {
  border-color: #ef4444 !important;
  background:
    linear-gradient(
      135deg,
      #fee2e2,
      #fecaca
    ) !important;
  color: #991b1b !important;

  box-shadow:
    0 0 0 3px rgba(239, 68, 68, 0.1);
}


/* ========================================================= */
/* СООБЩЕНИЯ */
/* ========================================================= */

.result-message {
  border: 2px solid transparent;
}


.correct-message {
  border-color: #86efac;
  background: #f0fdf4;
}


.wrong-message {
  border-color: #fca5a5;
  background: #fef2f2;
}


/* ========================================================= */
/* ЛАПКА */
/* ========================================================= */

.paw-wrapper {
  width: 92px;
  height: 92px;

  transform-origin:
    100% 50%;
}


.paw-push {
  position: relative;

  width: 100%;
  height: 100%;

  animation:
    paw-push 0.72s
    cubic-bezier(0.18, 0.8, 0.2, 1)
    infinite;
}


.paw-svg {
  width: 92px;
  height: 92px;

  display: block;

  filter:
    drop-shadow(
      0 5px 5px
      rgba(91, 48, 20, 0.3)
    );

  transform-origin:
    75% 80%;

  animation:
    paw-life 0.8s
    ease-in-out
    infinite;
}


/* ========================================================= */
/* ИСКОРКИ */
/* ========================================================= */

.paw-spark {
  position: absolute;

  pointer-events: none;

  z-index: 5;

  color: #f59e0b;

  font-weight: 1000;

  text-shadow:
    0 0 6px
    rgba(245, 158, 11, 0.65);
}


.spark-one {
  top: 1px;
  right: 5px;

  font-size: 17px;

  animation:
    sparkle-one
    0.75s
    ease-in-out
    infinite;
}


.spark-two {
  top: 30px;
  right: -4px;

  font-size: 12px;

  animation:
    sparkle-two
    0.9s
    ease-in-out
    infinite;
}


.spark-three {
  bottom: 12px;
  left: 1px;

  font-size: 14px;

  animation:
    sparkle-three
    0.85s
    ease-in-out
    infinite;
}


.spark-four {
  top: 12px;
  left: 3px;

  font-size: 10px;

  animation:
    sparkle-four
    0.7s
    ease-in-out
    infinite;
}


/* ========================================================= */
/* ПОЯВЛЕНИЕ ЛАПКИ */
/* ========================================================= */

.paw-enter-active {
  animation:
    paw-enter
    0.75s
    cubic-bezier(0.16, 1, 0.3, 1);
}


.paw-leave-active {
  animation:
    paw-leave
    0.45s
    ease-in;
}


/* ========================================================= */
/* ЛАПКА ВЫПРЫГИВАЕТ И ОТТЯГИВАЕТ ОТВЕТ */
/* ========================================================= */

@keyframes paw-enter {

  0% {
    opacity: 0;

    transform:
      translate(70px, -50%)
      rotate(35deg)
      scale(0.35);
  }

  35% {
    opacity: 1;

    transform:
      translate(8px, -50%)
      rotate(-12deg)
      scale(1.15);
  }

  55% {
    transform:
      translate(-7px, -50%)
      rotate(8deg)
      scale(1.05);
  }

  72% {
    transform:
      translate(3px, -50%)
      rotate(-4deg)
      scale(1.02);
  }

  100% {
    opacity: 1;

    transform:
      translate(0, -50%)
      rotate(0deg)
      scale(1);
  }
}


@keyframes paw-leave {

  0% {
    opacity: 1;

    transform:
      translate(0, -50%)
      rotate(0deg)
      scale(1);
  }

  100% {
    opacity: 0;

    transform:
      translate(65px, -50%)
      rotate(20deg)
      scale(0.45);
  }
}


/* ========================================================= */
/* АКТИВНАЯ ЛАПКА */
/* ========================================================= */

@keyframes paw-push {

  0%,
  100% {
    transform:
      translateX(0)
      rotate(0deg);
  }

  30% {
    transform:
      translateX(-8px)
      rotate(-7deg);
  }

  55% {
    transform:
      translateX(2px)
      rotate(5deg);
  }

  75% {
    transform:
      translateX(-4px)
      rotate(-3deg);
  }
}


@keyframes paw-life {

  0%,
  100% {
    transform:
      rotate(-5deg)
      translateY(0)
      scale(1);
  }

  50% {
    transform:
      rotate(7deg)
      translateY(-4px)
      scale(1.05);
  }
}


/* ========================================================= */
/* ИСКОРКИ АНИМАЦИЯ */
/* ========================================================= */

@keyframes sparkle-one {

  0%,
  100% {
    opacity: 0.2;

    transform:
      scale(0.5)
      rotate(0deg);
  }

  50% {
    opacity: 1;

    transform:
      scale(1.35)
      rotate(25deg);
  }
}


@keyframes sparkle-two {

  0%,
  100% {
    opacity: 0.15;

    transform:
      translateY(2px)
      scale(0.6);
  }

  50% {
    opacity: 1;

    transform:
      translateY(-7px)
      scale(1.2);
  }
}


@keyframes sparkle-three {

  0%,
  100% {
    opacity: 0.2;

    transform:
      scale(0.6);
  }

  50% {
    opacity: 1;

    transform:
      scale(1.4);
  }
}


@keyframes sparkle-four {

  0%,
  100% {
    opacity: 0.15;

    transform:
      translate(-2px, 2px)
      scale(0.5);
  }

  50% {
    opacity: 1;

    transform:
      translate(3px, -4px)
      scale(1.3);
  }
}


/* ========================================================= */
/* АНИМАЦИЯ ПРАВИЛЬНОГО ОТВЕТА */
/* ========================================================= */

@keyframes correct-pulse {

  0% {
    transform: scale(1);
  }

  35% {
    transform: scale(1.025);
  }

  65% {
    transform: scale(0.995);
  }

  100% {
    transform: scale(1);
  }
}


/* ========================================================= */
/* КНОПКИ НАВИГАЦИИ */
/* ========================================================= */

.nav-button:hover:not(:disabled) {
  transform:
    translateY(-2px)
    scale(1.01);
}


/* ========================================================= */
/* МАЛЕНЬКИЕ ЭКРАНЫ */
/* ========================================================= */

@media (max-width: 480px) {

  .paw-wrapper {
    width: 76px;
    height: 76px;
  }

  .paw-svg {
    width: 76px;
    height: 76px;
  }

  .answer-button {
    padding-right: 68px;
  }

}


@media (max-width: 360px) {

  .paw-wrapper {
    width: 68px;
    height: 68px;
  }

  .paw-svg {
    width: 68px;
    height: 68px;
  }

  .answer-button {
    min-height: 58px;
    padding-left: 15px;
    padding-right: 60px;
  }

}

</style>
```
