```vue
<template>
  <div
    class="relative min-h-screen bg-gradient-to-br from-[#fff8ed] via-[#fdf2df] to-[#f7e5cf] px-3 py-5 sm:px-4 sm:py-8"
  >

    <!-- =====================================================
         ОСНОВНОЙ КОНТЕНТ
         ===================================================== -->
    <div class="mx-auto w-full max-w-2xl">

      <!-- ===================================================
           ОСНОВНОЙ БЛОК
           =================================================== -->
      <div
        class="
          quiz-card
          w-full
          bg-[#fffdf9]
          rounded-2xl
          shadow-2xl
          border
          border-[#e5c9a7]
          p-4
          sm:p-6
          md:p-8
        "
      >

        <!-- ДЕКОРАТИВНАЯ ВЕРХНЯЯ ПОЛОСКА -->
        <div class="top-decoration">
          <span></span>
          <span></span>
          <span></span>
        </div>

        <!-- ЗАГОЛОВОК -->
        <h2
          class="
            text-center
            text-2xl
            sm:text-3xl
            font-extrabold
            text-[#6f3f25]
            mb-2
          "
        >
          Тест: Ударение
        </h2>

        <!-- ИНСТРУКЦИЯ -->
        <p
          class="
            text-center
            text-[#725f50]
            mb-5
            sm:mb-6
            text-sm
            sm:text-base
            leading-relaxed
          "
        >
          В каком слове буква, обозначающая ударный гласный, выделена верно?
        </p>

        <!-- =================================================
             ТЕСТ
             ================================================= -->
        <div v-if="!finished">

          <!-- НОМЕР ВОПРОСА -->
          <div
            class="
              mb-4
              text-center
              text-base
              sm:text-lg
              font-bold
              text-[#87502f]
            "
          >
            Вопрос {{ currentIdx + 1 }} из {{ questions.length }}
          </div>

          <!-- =================================================
               ВАРИАНТЫ ОТВЕТА
               ================================================= -->
          <div class="flex w-full flex-col gap-3 mb-5 sm:mb-6">

            <button
              v-for="(option, idx) in currentQuestion.options"
              :key="idx"
              type="button"
              @click="selectOption(idx)"
              :disabled="showResult"
              class="
                answer-button
                relative
                w-full
                min-w-0
                min-h-[54px]
                rounded-xl
                border-2
                py-3
                pl-4
                pr-16
                text-left
                text-base
                sm:text-lg
                font-semibold
                leading-relaxed
                break-words
                whitespace-normal
                transition-all
                duration-300
                disabled:cursor-default
              "
              :class="[

                /* Обычный вариант */
                !showResult
                  ? 'bg-[#fffaf3] border-[#e6d5c0] text-[#4d4036] hover:border-[#c99b6c] hover:bg-[#fff5e8]'
                  : '',

                /* Выбранный до проверки */
                selectedIdx === idx && !showResult
                  ? 'bg-[#e7f7d5] border-[#91c95d]'
                  : '',

                /* =================================================
                   ПРАВИЛЬНЫЙ ОТВЕТ
                   ================================================= */
                showResult &&
                idx === currentQuestion.correct
                  ? 'correct-answer'
                  : '',

                /* =================================================
                   НЕПРАВИЛЬНЫЙ ВЫБРАННЫЙ ОТВЕТ
                   ================================================= */
                showResult &&
                selectedIdx === idx &&
                selectedIdx !== currentQuestion.correct
                  ? 'wrong-answer'
                  : ''
              ]"
            >

              <!-- ТЕКСТ ОТВЕТА -->
              <span
                class="
                  relative
                  z-10
                  block
                  w-full
                  break-words
                  whitespace-normal
                "
              >
                {{ option }}
              </span>

              <!-- =================================================
                   ЛАПКА ЛЕОПАРДА

                   Она выскакивает справа и визуально
                   "цепляется" за правильный ответ.
                   ================================================= -->
              <Transition name="paw">

                <div
                  v-if="
                    showPaw &&
                    idx === currentQuestion.correct &&
                    selectedIdx === currentQuestion.correct
                  "
                  class="
                    leopard-paw
                    pointer-events-none
                    absolute
                    right-[-18px]
                    top-1/2
                    z-30
                  "
                  aria-hidden="true"
                >

                  <!-- ЛАПКА -->
                  <svg
                    viewBox="0 0 130 110"
                    class="paw-svg"
                    xmlns="http://www.w3.org/2000/svg"
                  >

                    <defs>

                      <!-- =================================================
                           МЕХ
                           ================================================= -->
                      <radialGradient
                        id="pawFurBright"
                        cx="35%"
                        cy="25%"
                        r="80%"
                      >
                        <stop
                          offset="0%"
                          stop-color="#ffe8b8"
                        />

                        <stop
                          offset="28%"
                          stop-color="#f4cf8e"
                        />

                        <stop
                          offset="62%"
                          stop-color="#d89d58"
                        />

                        <stop
                          offset="100%"
                          stop-color="#9c5c2e"
                        />
                      </radialGradient>

                      <!-- =================================================
                           РОЗОВЫЕ ПОДУШЕЧКИ
                           ================================================= -->
                      <radialGradient
                        id="pawPadBright"
                        cx="35%"
                        cy="25%"
                        r="80%"
                      >
                        <stop
                          offset="0%"
                          stop-color="#fff0f0"
                        />

                        <stop
                          offset="40%"
                          stop-color="#ffc9d0"
                        />

                        <stop
                          offset="75%"
                          stop-color="#ef98a7"
                        />

                        <stop
                          offset="100%"
                          stop-color="#c96c7f"
                        />
                      </radialGradient>

                      <!-- =================================================
                           ТЕНЬ
                           ================================================= -->
                      <filter
                        id="pawShadowStrong"
                        x="-50%"
                        y="-50%"
                        width="220%"
                        height="220%"
                      >
                        <feDropShadow
                          dx="2"
                          dy="5"
                          stdDeviation="3"
                          flood-color="#58321f"
                          flood-opacity="0.38"
                        />
                      </filter>

                      <!-- =================================================
                           БЛИК
                           ================================================= -->
                      <linearGradient
                        id="furHighlight"
                        x1="0"
                        y1="0"
                        x2="1"
                        y2="1"
                      >
                        <stop
                          offset="0%"
                          stop-color="#fff4d8"
                          stop-opacity="0.8"
                        />

                        <stop
                          offset="100%"
                          stop-color="#fff4d8"
                          stop-opacity="0"
                        />
                      </linearGradient>

                    </defs>

                    <!-- =================================================
                         ВСЯ ЛАПКА
                         ================================================= -->
                    <g
                      filter="url(#pawShadowStrong)"
                      transform="rotate(-10 65 55)"
                    >

                      <!-- =================================================
                           ОСНОВА
                           ================================================= -->
                      <path
                        d="
                          M43 52
                          C33 55 28 66 31 78
                          C34 91 46 99 61 100
                          C78 101 95 94 99 81
                          C103 68 96 55 85 51
                          C73 47 54 47 43 52Z
                        "
                        fill="url(#pawFurBright)"
                        stroke="#713d20"
                        stroke-width="2.5"
                      />

                      <!-- =================================================
                           ЦЕНТРАЛЬНАЯ ПОДУШЕЧКА
                           ================================================= -->
                      <path
                        d="
                          M48 63
                          C41 67 41 77 46 83
                          C51 89 61 90 69 86
                          C77 82 79 73 75 67
                          C71 61 58 59 48 63Z
                        "
                        fill="url(#pawPadBright)"
                        stroke="#bd6879"
                        stroke-width="1.6"
                      />

                      <!-- =================================================
                           БОЛЬШОЙ БОКОВОЙ ПАЛЕЦ
                           ================================================= -->
                      <ellipse
                        cx="91"
                        cy="57"
                        rx="13"
                        ry="17"
                        transform="rotate(24 91 57)"
                        fill="url(#pawFurBright)"
                        stroke="#713d20"
                        stroke-width="2.5"
                      />

                      <ellipse
                        cx="91"
                        cy="58"
                        rx="7"
                        ry="9"
                        transform="rotate(24 91 58)"
                        fill="url(#pawPadBright)"
                      />

                      <!-- =================================================
                           ЧЕТЫРЕ ПАЛЬЧИКА
                           ================================================= -->

                      <ellipse
                        cx="32"
                        cy="43"
                        rx="10"
                        ry="17"
                        transform="rotate(-30 32 43)"
                        fill="url(#pawFurBright)"
                        stroke="#713d20"
                        stroke-width="2.5"
                      />

                      <ellipse
                        cx="50"
                        cy="32"
                        rx="10"
                        ry="18"
                        transform="rotate(-13 50 32)"
                        fill="url(#pawFurBright)"
                        stroke="#713d20"
                        stroke-width="2.5"
                      />

                      <ellipse
                        cx="68"
                        cy="31"
                        rx="10"
                        ry="18"
                        transform="rotate(9 68 31)"
                        fill="url(#pawFurBright)"
                        stroke="#713d20"
                        stroke-width="2.5"
                      />

                      <ellipse
                        cx="84"
                        cy="40"
                        rx="10"
                        ry="17"
                        transform="rotate(27 84 40)"
                        fill="url(#pawFurBright)"
                        stroke="#713d20"
                        stroke-width="2.5"
                      />

                      <!-- =================================================
                           ПОДУШЕЧКИ ПАЛЬЦЕВ
                           ================================================= -->

                      <ellipse
                        cx="32"
                        cy="43"
                        rx="6"
                        ry="8"
                        transform="rotate(-30 32 43)"
                        fill="url(#pawPadBright)"
                      />

                      <ellipse
                        cx="50"
                        cy="32"
                        rx="6"
                        ry="9"
                        transform="rotate(-13 50 32)"
                        fill="url(#pawPadBright)"
                      />

                      <ellipse
                        cx="68"
                        cy="31"
                        rx="6"
                        ry="9"
                        transform="rotate(9 68 31)"
                        fill="url(#pawPadBright)"
                      />

                      <ellipse
                        cx="84"
                        cy="40"
                        rx="6"
                        ry="8"
                        transform="rotate(27 84 40)"
                        fill="url(#pawPadBright)"
                      />

                      <!-- =================================================
                           ЛЕОПАРДОВЫЕ РОЗЕТКИ
                           ================================================= -->

                      <!-- Большая розетка -->
                      <g
                        fill="none"
                        stroke="#633618"
                        stroke-width="3"
                        stroke-linecap="round"
                      >
                        <path
                          d="M42 70 C38 66 42 62 47 64 C51 66 50 71 46 73 C44 74 43 72 42 70Z"
                        />

                        <path
                          d="M61 63 C57 60 60 56 65 57 C70 58 71 63 67 66 C65 68 62 66 61 63Z"
                        />

                        <path
                          d="M76 75 C73 71 77 68 82 70 C86 72 85 77 81 79 C78 80 76 78 76 75Z"
                        />

                        <path
                          d="M53 87 C49 84 52 81 56 82 C60 83 61 87 58 89 C56 91 54 89 53 87Z"
                        />
                      </g>

                      <!-- Центры розеток -->
                      <g
                        fill="#75401d"
                      >
                        <ellipse
                          cx="44"
                          cy="69"
                          rx="2.2"
                          ry="2.8"
                        />

                        <ellipse
                          cx="64"
                          cy="62"
                          rx="2"
                          ry="2.5"
                        />

                        <ellipse
                          cx="79"
                          cy="75"
                          rx="2.2"
                          ry="2.7"
                        />

                        <ellipse
                          cx="56"
                          cy="86"
                          rx="1.8"
                          ry="2.3"
                        />
                      </g>

                      <!-- =================================================
                           ПЯТНЫШКИ НА ПАЛЬЦАХ
                           ================================================= -->

                      <g fill="#633618">

                        <circle
                          cx="27"
                          cy="34"
                          r="2.6"
                        />

                        <circle
                          cx="38"
                          cy="27"
                          r="2.1"
                        />

                        <circle
                          cx="48"
                          cy="21"
                          r="2.5"
                        />

                        <circle
                          cx="58"
                          cy="25"
                          r="2"
                        />

                        <circle
                          cx="72"
                          cy="21"
                          r="2.4"
                        />

                        <circle
                          cx="82"
                          cy="30"
                          r="2.6"
                        />

                        <circle
                          cx="94"
                          cy="45"
                          r="2.3"
                        />

                        <circle
                          cx="88"
                          cy="67"
                          r="2.1"
                        />

                      </g>

                      <!-- =================================================
                           МЕЛКИЕ ПЯТНА
                           ================================================= -->

                      <g fill="#82461f">

                        <ellipse
                          cx="36"
                          cy="58"
                          rx="3"
                          ry="2"
                          transform="rotate(-20 36 58)"
                        />

                        <ellipse
                          cx="83"
                          cy="58"
                          rx="3"
                          ry="2"
                          transform="rotate(25 83 58)"
                        />

                        <ellipse
                          cx="70"
                          cy="90"
                          rx="3"
                          ry="2"
                          transform="rotate(-15 70 90)"
                        />

                        <ellipse
                          cx="96"
                          cy="76"
                          rx="3"
                          ry="2"
                          transform="rotate(30 96 76)"
                        />

                      </g>

                      <!-- =================================================
                           МЯГКИЕ БЛИКИ МЕХА
                           ================================================= -->

                      <ellipse
                        cx="43"
                        cy="57"
                        rx="9"
                        ry="4"
                        fill="url(#furHighlight)"
                        transform="rotate(-25 43 57)"
                      />

                      <ellipse
                        cx="63"
                        cy="50"
                        rx="8"
                        ry="3"
                        fill="#fff4d8"
                        opacity="0.35"
                        transform="rotate(12 63 50)"
                      />

                    </g>

                  </svg>

                  <!-- =================================================
                       ИСКОРКИ
                       ================================================= -->
                  <span class="paw-spark spark-one">✦</span>
                  <span class="paw-spark spark-two">✦</span>
                  <span class="paw-spark spark-three">•</span>
                  <span class="paw-spark spark-four">✦</span>

                </div>

              </Transition>

            </button>

          </div>

          <!-- =================================================
               РЕЗУЛЬТАТ ОТВЕТА
               ================================================= -->
          <div
            v-if="showResult"
            class="
              mt-3
              text-center
              min-w-0
            "
          >

            <!-- ПРАВИЛЬНО -->
            <div
              v-if="selectedIdx === currentQuestion.correct"
              class="
                correct-message
                inline-flex
                items-center
                justify-center
                gap-2
                rounded-full
                px-5
                py-2
                text-green-800
                font-bold
                text-sm
                sm:text-base
              "
            >
              <span class="check-bubble">✓</span>
              Верно!
            </div>

            <!-- НЕПРАВИЛЬНО -->
            <div
              v-else
              class="
                text-red-600
                font-semibold
                text-sm
                sm:text-base
                leading-relaxed
                break-words
              "
            >
              ❌ Неверно. Правильный ответ:
              <b>
                {{ currentQuestion.options[currentQuestion.correct] }}
              </b>
            </div>

          </div>

          <!-- =================================================
               НАВИГАЦИЯ
               ================================================= -->
          <div
            v-if="showResult"
            class="
              mt-4
              flex
              flex-col
              sm:flex-row
              gap-3
            "
          >

            <button
              type="button"
              @click="prevQuestion"
              :disabled="currentIdx === 0"
              class="
                w-full
                sm:flex-1
                min-h-[48px]
                bg-[#eadbc9]
                hover:bg-[#dfc8ad]
                text-[#67442f]
                font-bold
                py-3
                px-4
                rounded-xl
                transition
                disabled:opacity-50
                disabled:cursor-not-allowed
              "
            >
              ⬅️ Назад
            </button>

            <button
              type="button"
              @click="nextQuestion"
              :disabled="currentIdx >= questions.length - 1"
              class="
                w-full
                sm:flex-1
                min-h-[48px]
                bg-[#7a4328]
                hover:bg-[#63351f]
                text-white
                font-bold
                py-3
                px-4
                rounded-xl
                shadow-md
                transition
                disabled:opacity-50
                disabled:cursor-not-allowed
              "
            >
              Вперёд ➡️
            </button>

          </div>

        </div>

        <!-- =================================================
             ФИНАЛЬНЫЙ РЕЗУЛЬТАТ
             ================================================= -->
        <div
          v-else
          class="text-center mt-4 sm:mt-6"
        >

          <div
            class="
              bg-[#fff4e5]
              border
              border-[#e7cbaa]
              rounded-2xl
              shadow-lg
              p-4
              sm:p-8
            "
          >

            <p
              class="
                text-2xl
                sm:text-3xl
                font-extrabold
                text-[#744329]
                mb-3
              "
            >
              🎉 Ваши результаты
            </p>

            <p
              class="
                text-base
                sm:text-lg
                text-[#806b59]
                mb-5
                sm:mb-6
              "
            >
              Вы выполнили задание «Ударение»
            </p>

            <div
              class="
                bg-white
                border
                border-[#ead8c3]
                rounded-xl
                p-4
                sm:p-6
                shadow-md
                mb-5
              "
            >

              <p class="text-[#806b59] mb-2">
                Результат
              </p>

              <p
                class="
                  text-4xl
                  sm:text-5xl
                  font-extrabold
                  text-[#65ad32]
                  mb-3
                "
              >
                {{ correctCount }}/{{ questions.length }}
              </p>

              <p
                class="
                  text-2xl
                  sm:text-3xl
                  font-bold
                  text-[#6f4630]
                  mb-3
                "
              >
                {{ percent }}%
              </p>

              <p class="text-lg sm:text-xl font-semibold text-[#594638]">
                Оценка:
                <span :class="gradeColor">
                  {{ grade }}
                </span>
              </p>

            </div>

            <!-- СОХРАНЕНИЕ -->
            <p
              v-if="saveStatus === 'saving'"
              class="text-blue-600 mb-4"
            >
              Сохраняем результат...
            </p>

            <p
              v-if="saveStatus === 'saved'"
              class="text-green-600 font-semibold mb-4"
            >
              ✅ Результат сохранён
            </p>

            <p
              v-if="saveStatus === 'error'"
              class="text-red-600 font-semibold mb-4"
            >
              ❌ Не удалось сохранить результат
            </p>

            <!-- КНОПКИ -->
            <div class="flex flex-col gap-3 mt-5">

              <NuxtLink
                to="/results"
                class="
                  block
                  bg-[#7a4328]
                  hover:bg-[#63351f]
                  text-white
                  font-semibold
                  py-3
                  px-5
                  rounded-xl
                  shadow-md
                  transition
                  text-center
                "
              >
                📊 Мой прогресс
              </NuxtLink>

              <NuxtLink
                to="/"
                class="
                  block
                  bg-[#eadbc9]
                  hover:bg-[#dfc8ad]
                  text-[#67442f]
                  font-semibold
                  py-3
                  px-5
                  rounded-xl
                  transition
                  text-center
                "
              >
                ⬅️ На главную
              </NuxtLink>

            </div>

          </div>

        </div>

      </div>

      <!-- =====================================================
           ОКОШКО С НОМЕРАМИ ВОПРОСОВ
           ===================================================== -->
      <div
        v-if="!finished"
        class="
          question-panel
          mt-4
          w-full
          shadow-xl
          rounded-2xl
          p-3
          sm:p-4

          md:absolute
          md:top-4
          md:right-4
          md:mt-0
          md:w-72
        "
      >

        <!-- ЗАГОЛОВОК ПАНЕЛИ -->
        <div
          class="
            flex
            items-center
            justify-center
            gap-2
            mb-3
            text-[#70432a]
            font-bold
          "
        >
          <span class="paw-mini">🐾</span>
          <span>Вопросы</span>
        </div>

        <div
          class="
            grid
            grid-cols-5
            gap-2
            sm:gap-3
            justify-items-center
          "
        >

          <button
            v-for="(q, i) in questions"
            :key="i"
            type="button"
            @click="jumpTo(i)"
            class="
              question-number
              w-9
              h-9
              sm:w-10
              sm:h-10
              flex
              items-center
              justify-center
              rounded-lg
              text-sm
              sm:text-base
              font-bold
              transition
              duration-200
            "
            :class="[

              /* ТЕКУЩИЙ */
              currentIdx === i
                ? 'current-number'

                /* ОТВЕЧЕН */
                : answered[i]
                  ? 'answered-number'

                  /* НЕ ОТВЕЧЕН */
                  : 'empty-number'
            ]"
          >
            {{ i + 1 }}
          </button>

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

const supabase = useSupabaseClient()

/* =========================================================
   ВОПРОСЫ
   ========================================================= */

const questions = [
  {
    options: [
      'индУстрия',
      'кухОнный',
      'ходатАйство',
      'закУпорить'
    ],
    correct: 3
  },

  {
    options: [
      'Посадить ирИс',
      'знамЕние',
      'балОванный',
      'звОнит'
    ],
    correct: 2
  },

  {
    options: [
      'КвАртал',
      'катАлог',
      'газирОванный',
      'премИровать'
    ],
    correct: 2
  },

  {
    options: [
      'ОблЕгчить',
      'дОсуг',
      'икОнопись',
      'кладовАя'
    ],
    correct: 3
  },

  {
    options: [
      'ХристианИн',
      'апОстроф',
      'генЕзис',
      'танцовщИк'
    ],
    correct: 0
  },

  {
    options: [
      'газИрованный',
      'анАтом',
      'дОговор',
      'зАвидно'
    ],
    correct: 1
  },

  {
    options: [
      'Оптовый',
      'наотмАшь',
      'мИзерный',
      'кулинАрия'
    ],
    correct: 3
  },

  {
    options: [
      'призывнОй',
      'призывнЫй',
      'досытА',
      'агрономИя'
    ],
    correct: 0
  },

  {
    options: [
      'бармЕн',
      'гастронОмия',
      'издавнА',
      'крЕмень'
    ],
    correct: 1
  },

  {
    options: [
      'пОутру',
      'мелькОм',
      'бородУ',
      'бомбардировАть'
    ],
    correct: 3
  },

  {
    options: [
      'дЕспот',
      'глАдильный',
      'дремотА',
      'бАлуясь'
    ],
    correct: 0
  },

  {
    options: [
      'истЕрия',
      'зубчАтый',
      'олигархИя',
      'рАкушка'
    ],
    correct: 1
  },

  {
    options: [
      'обеспечЕние',
      'веровАние',
      'избрАнник',
      'индУстрия'
    ],
    correct: 2
  },

  {
    options: [
      'пАмятуя',
      'сАжень',
      'сАбо',
      'прОстыня'
    ],
    correct: 0
  },

  {
    options: [
      'кладОвая',
      'забЕленный',
      'рАдушный',
      'кружАщий'
    ],
    correct: 3
  }
]

/* =========================================================
   СОСТОЯНИЕ
   ========================================================= */

const currentIdx = ref(0)

const selectedIdx = ref<number | null>(null)

const showResult = ref(false)

const correctCount = ref(0)

const userAnswers = ref<(number | null)[]>(
  Array(questions.length).fill(null)
)

const answered = ref<boolean[]>(
  Array(questions.length).fill(false)
)

/* =========================================================
   ЛАПКА
   ========================================================= */

const showPaw = ref(false)

let pawTimer: ReturnType<typeof setTimeout> | null = null

function triggerCorrectPaw() {

  if (pawTimer) {
    clearTimeout(pawTimer)
  }

  /*
   * Сначала выключаем лапку,
   * чтобы Transition заново запустил
   * эффект даже при повторном выборе.
   */
  showPaw.value = false

  requestAnimationFrame(() => {
    showPaw.value = true
  })

  /*
   * Лапка живёт 3 секунды.
   */
  pawTimer = setTimeout(() => {

    showPaw.value = false

    pawTimer = null

  }, 3000)
}

onBeforeUnmount(() => {

  if (pawTimer) {
    clearTimeout(pawTimer)
  }

})

/* =========================================================
   ДАННЫЕ УЧЕНИКА
   ========================================================= */

const studentName = ref('')

const studentClass = ref('')

const startedAt = ref('')

const attemptId = ref<string | null>(null)

const saveStatus = ref<
  'saving' | 'saved' | 'error' | null
>(null)

/* =========================================================
   COMPUTED
   ========================================================= */

const currentQuestion = computed(() => {
  return questions[currentIdx.value]
})

const finished = computed(() => {
  return answered.value.every(a => a)
})

const percent = computed(() => {

  return Math.round(
    (correctCount.value / questions.length) * 100
  )

})

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

/* =========================================================
   ЗАГРУЗКА УЧЕНИКА
   ========================================================= */

onMounted(async () => {

  const savedStudent =
    localStorage.getItem('orphoepia_student')

  if (!savedStudent) {

    await navigateTo('/')

    return
  }

  try {

    const student =
      JSON.parse(savedStudent)

    studentName.value =
      student.name || ''

    studentClass.value =
      student.class || ''

    if (
      !studentName.value ||
      !studentClass.value
    ) {

      await navigateTo('/')

      return
    }

  } catch {

    await navigateTo('/')

    return
  }

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
          'Ударение',

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
})

/* =========================================================
   ВЫБОР ОТВЕТА
   ========================================================= */

function selectOption(idx: number) {

  if (showResult.value) return

  selectedIdx.value =
    idx

  userAnswers.value[
    currentIdx.value
  ] = idx

  showResult.value =
    true

  answered.value[
    currentIdx.value
  ] = true

  /* =======================================================
     ПРАВИЛЬНЫЙ ОТВЕТ
     ======================================================= */

  if (
    idx ===
    currentQuestion.value.correct
  ) {

    correctCount.value++

    /*
     * Активная лапка.
     */
    triggerCorrectPaw()
  }

  /*
   * Если последний вопрос —
   * сохраняем результат.
   */
  if (finished.value) {

    saveResult()

  }
}

/* =========================================================
   СОХРАНЕНИЕ РЕЗУЛЬТАТА
   ========================================================= */

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

/* =========================================================
   СЛЕДУЮЩИЙ ВОПРОС
   ========================================================= */

function nextQuestion() {

  if (
    currentIdx.value <
    questions.length - 1
  ) {

    showPaw.value =
      false

    currentIdx.value++

    selectedIdx.value =
      userAnswers.value[
        currentIdx.value
      ]

    showResult.value =
      answered.value[
        currentIdx.value
      ]
  }
}

/* =========================================================
   ПРЕДЫДУЩИЙ ВОПРОС
   ========================================================= */

function prevQuestion() {

  if (
    currentIdx.value > 0
  ) {

    showPaw.value =
      false

    currentIdx.value--

    selectedIdx.value =
      userAnswers.value[
        currentIdx.value
      ]

    showResult.value =
      answered.value[
        currentIdx.value
      ]
  }
}

/* =========================================================
   ПЕРЕХОД К ВОПРОСУ
   ========================================================= */

function jumpTo(i: number) {

  showPaw.value =
    false

  currentIdx.value =
    i

  selectedIdx.value =
    userAnswers.value[i]

  showResult.value =
    answered.value[i]
}
</script>

<style scoped>

/* =========================================================
   ОСНОВНАЯ КАРТОЧКА
   ========================================================= */

.quiz-card {
  position: relative;
  overflow: visible;
}

/* =========================================================
   ДЕКОР
   ========================================================= */

.top-decoration {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 6px;
  margin-bottom: 10px;
}

.top-decoration span {
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: #d79a55;
}

.top-decoration span:nth-child(2) {
  width: 11px;
  height: 11px;
  background: #7a4328;
}

/* =========================================================
   КНОПКИ ОТВЕТОВ
   ========================================================= */

.answer-button {
  overflow: visible;

  padding-right: 4.5rem;

  overflow-wrap: anywhere;
  word-break: normal;

  transform-origin: center left;
}

/* =========================================================
   ПРАВИЛЬНЫЙ ОТВЕТ
   ========================================================= */

.correct-answer {
  background:
    linear-gradient(
      90deg,
      #b8e875 0%,
      #9fda5b 55%,
      #8bce4a 100%
    );

  border-color: #69ad2d;

  color: #294d18;

  box-shadow:
    0 5px 15px rgba(93, 153, 39, 0.25),
    inset 0 1px 0 rgba(255,255,255,0.55);

  animation:
    correctAnswerPop
    0.65s
    cubic-bezier(0.2, 1.4, 0.4, 1);
}

/*
 * Ответ слегка "отдаёт" вправо,
 * когда лапка его хватает.
 */
@keyframes correctAnswerPop {

  0% {
    transform:
      translateX(0)
      scale(1);
  }

  30% {
    transform:
      translateX(-3px)
      scale(1.015);
  }

  55% {
    transform:
      translateX(2px)
      scale(1.025);
  }

  75% {
    transform:
      translateX(-1px)
      scale(1.01);
  }

  100% {
    transform:
      translateX(0)
      scale(1);
  }
}

/* =========================================================
   НЕПРАВИЛЬНЫЙ ОТВЕТ
   ========================================================= */

.wrong-answer {
  background: #ffe4e1;

  border-color: #ef8f86;

  color: #8c342d;

  box-shadow:
    0 4px 12px rgba(190, 70, 60, 0.14);
}

/* =========================================================
   СООБЩЕНИЕ "ВЕРНО"
   ========================================================= */

.correct-message {
  background: #e7f8d7;
  border: 1px solid #a9d77d;

  box-shadow:
    0 3px 10px rgba(95, 157, 45, 0.12);
}

.check-bubble {
  width: 23px;
  height: 23px;

  display: inline-flex;
  align-items: center;
  justify-content: center;

  border-radius: 50%;

  background: #69b52f;
  color: white;

  font-weight: 900;
}

/* =========================================================
   ЛАПКА
   ========================================================= */

.leopard-paw {
  width: 90px;
  height: 76px;

  transform:
    translate(0, -50%);

  transform-origin:
    75% 70%;
}

/*
 * На компьютере лапка крупнее.
 */
@media (min-width: 768px) {

  .leopard-paw {
    width: 104px;
    height: 88px;
  }

}

/* =========================================================
   SVG ЛАПКИ
   ========================================================= */

.paw-svg {
  width: 100%;
  height: 100%;

  display: block;

  overflow: visible;

  transform-origin:
    75% 75%;

  animation:
    pawGrab
    0.62s
    cubic-bezier(0.2, 1.35, 0.35, 1)
    0.42s
    infinite alternate;
}

/*
 * Лапка активно шевелится,
 * словно держит ответ.
 */
@keyframes pawGrab {

  0% {
    transform:
      rotate(-7deg)
      translateX(1px)
      translateY(0)
      scale(1);
  }

  35% {
    transform:
      rotate(5deg)
      translateX(-5px)
      translateY(-2px)
      scale(1.05);
  }

  70% {
    transform:
      rotate(-3deg)
      translateX(-2px)
      translateY(1px)
      scale(1.02);
  }

  100% {
    transform:
      rotate(4deg)
      translateX(-6px)
      translateY(-1px)
      scale(1.05);
  }
}

/* =========================================================
   ИСКОРКИ
   ========================================================= */

.paw-spark {
  position: absolute;

  color: #d99135;

  font-weight: 900;

  pointer-events: none;

  text-shadow:
    0 1px 3px rgba(115, 67, 28, 0.25);

  animation:
    sparkleActive
    0.65s
    ease-in-out
    infinite alternate;
}

.spark-one {
  top: -3px;
  left: 4px;
  font-size: 15px;
}

.spark-two {
  top: 12px;
  right: -2px;
  font-size: 11px;
  animation-delay: 0.15s;
}

.spark-three {
  bottom: 5px;
  left: -1px;
  font-size: 10px;
  animation-delay: 0.28s;
}

.spark-four {
  top: 28px;
  right: 4px;
  font-size: 8px;
  animation-delay: 0.4s;
}

@keyframes sparkleActive {

  0% {
    opacity: 0.25;
    transform:
      scale(0.65)
      rotate(0deg);
  }

  100% {
    opacity: 1;
    transform:
      scale(1.25)
      rotate(18deg);
  }
}

/* =========================================================
   ПОЯВЛЕНИЕ ЛАПКИ
   ========================================================= */

.paw-enter-active {

  animation:
    pawJumpIn
    0.62s
    cubic-bezier(0.12, 1.25, 0.25, 1);

}

@keyframes pawJumpIn {

  0% {
    opacity: 0;

    transform:
      translate(48px, -50%)
      rotate(28deg)
      scale(0.55);
  }

  25% {
    opacity: 1;

    transform:
      translate(12px, -50%)
      rotate(-18deg)
      scale(1.08);
  }

  48% {

    transform:
      translate(-7px, -50%)
      rotate(10deg)
      scale(1.15);
  }

  65% {

    transform:
      translate(4px, -50%)
      rotate(-7deg)
      scale(0.98);
  }

  82% {

    transform:
      translate(-4px, -50%)
      rotate(4deg)
      scale(1.06);
  }

  100% {
    opacity: 1;

    transform:
      translate(0, -50%)
      rotate(0deg)
      scale(1);
  }
}

/* =========================================================
   ИСЧЕЗНОВЕНИЕ
   ========================================================= */

.paw-leave-active {

  animation:
    pawJumpOut
    0.5s
    cubic-bezier(0.55, 0, 0.8, 0.3)
    forwards;

}

@keyframes pawJumpOut {

  0% {
    opacity: 1;

    transform:
      translate(0, -50%)
      rotate(0deg)
      scale(1);
  }

  25% {

    transform:
      translate(-6px, -50%)
      rotate(-8deg)
      scale(1.05);
  }

  100% {
    opacity: 0;

    transform:
      translate(50px, -50%)
      rotate(25deg)
      scale(0.65);
  }
}

/* =========================================================
   ОКНО НОМЕРОВ
   ========================================================= */

.question-panel {

  background:
    linear-gradient(
      145deg,
      #fff4e2,
      #f3ddc1
    );

  border:
    2px solid #d5ad80;

  box-shadow:
    0 8px 22px rgba(103, 63, 34, 0.16),
    inset 0 1px 0 rgba(255,255,255,0.75);
}

/* =========================================================
   МАЛЕНЬКАЯ ЛАПКА В ЗАГОЛОВКЕ
   ========================================================= */

.paw-mini {
  font-size: 17px;

  color: #7a4328;
}

/* =========================================================
   НОМЕРА ВОПРОСОВ
   ========================================================= */

.question-number {
  border: 1px solid transparent;
}

/* ТЕКУЩИЙ */
.current-number {

  background:
    linear-gradient(
      145deg,
      #8c4f2e,
      #6d3a23
    );

  color: white;

  border-color: #60321e;

  box-shadow:
    0 4px 9px rgba(104, 57, 32, 0.3);

  transform:
    scale(1.08);
}

/* ОТВЕЧЕННЫЙ */
.answered-number {

  background:
    linear-gradient(
      145deg,
      #a9df69,
      #82c84b
    );

  color: white;

  border-color: #70ae3d;

  box-shadow:
    0 3px 7px rgba(93, 153, 39, 0.18);
}

/* НЕ ОТВЕЧЕННЫЙ */
.empty-number {

  background:
    #f9eee0;

  color: #765f4d;

  border-color:
    #e4d1b9;
}

@media (hover: hover) {

  .empty-number:hover {

    background:
      #f1ddc5;

    border-color:
      #c99b6c;

    transform:
      translateY(-1px);

  }

}

/* =========================================================
   МОБИЛЬНАЯ ВЕРСИЯ
   ========================================================= */

@media (max-width: 480px) {

  .answer-button {

    padding-right:
      4rem;

    min-height:
      54px;

  }

  .leopard-paw {

    right:
      -13px;

    width:
      78px;

    height:
      67px;

  }

}

/* =========================================================
   ОЧЕНЬ МАЛЕНЬКИЙ ЭКРАН
   ========================================================= */

@media (max-width: 360px) {

  .answer-button {

    min-height:
      52px;

    font-size:
      15px;

    padding-left:
      12px;

    padding-right:
      3.6rem;

  }

  .leopard-paw {

    right:
      -10px;

    width:
      69px;

    height:
      60px;

  }

  .question-panel {

    padding:
      10px;

  }

  .question-number {

    width:
      34px;

    height:
      34px;

    font-size:
      13px;

  }

}

</style>
```
