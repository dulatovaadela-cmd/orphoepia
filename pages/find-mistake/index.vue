<template>
  <div
    class="relative min-h-screen bg-gradient-to-br from-amber-100 via-pink-50 to-purple-100 px-4 py-6 sm:px-6 lg:px-8"
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
          <div class="relative z-10 mb-6 flex justify-center">
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

          <!-- ================================================= -->
          <!-- ПОЛЕ ОТВЕТА + ЛАПКА -->
          <!-- ================================================= -->

          <div class="relative z-10 mx-auto max-w-2xl">

            <label
              class="mb-3 block text-center text-base font-extrabold text-purple-700 sm:text-lg"
            >
              ✏️ Твой ответ
            </label>

            <div class="relative flex items-center gap-2">

              <!-- ПОЛЕ ОТВЕТА -->
              <input
                v-model="userAnswer"
                type="text"
                autocomplete="off"
                :disabled="showResult"
                placeholder="Напиши правильное ударение..."
                :class="[
                  'h-14 min-w-0 flex-1 rounded-2xl border-3 px-5 text-center text-lg font-bold shadow-lg outline-none transition-all duration-300 placeholder:text-sm placeholder:font-medium sm:text-xl',

                  showResult && isCorrect
                    ? 'border-green-500 bg-green-100 text-green-700 shadow-[0_0_20px_rgba(34,197,94,0.35)]'
                    : showResult && !isCorrect
                      ? 'border-red-300 bg-red-50 text-red-600'
                      : 'border-purple-200 bg-white text-gray-800 focus:border-purple-500 focus:ring-4 focus:ring-purple-100 disabled:bg-gray-50'
                ]"
                @keyup.enter="checkAnswer"
              />

              <!-- ================================================= -->
              <!-- АКТИВНАЯ ЛЕОПАРДОВАЯ ЛАПКА -->
              <!-- ================================================= -->

              <transition name="paw-attack">
                <div
                  v-if="showPaw"
                  class="paw-wrapper pointer-events-none absolute -right-5 top-1/2 z-30 -translate-y-1/2 sm:-right-9"
                >
                  <svg
                    class="paw-svg"
                    viewBox="0 0 120 120"
                    xmlns="http://www.w3.org/2000/svg"
                  >
                    <defs>

                      <!-- Мех -->
                      <radialGradient
                        id="pawFur"
                        cx="35%"
                        cy="20%"
                        r="85%"
                      >
                        <stop
                          offset="0%"
                          stop-color="#ffe29a"
                        />
                        <stop
                          offset="25%"
                          stop-color="#f4c15e"
                        />
                        <stop
                          offset="55%"
                          stop-color="#d99836"
                        />
                        <stop
                          offset="80%"
                          stop-color="#b86f24"
                        />
                        <stop
                          offset="100%"
                          stop-color="#754116"
                        />
                      </radialGradient>

                      <!-- Розовые подушечки -->
                      <radialGradient
                        id="pawPad"
                        cx="30%"
                        cy="20%"
                        r="85%"
                      >
                        <stop
                          offset="0%"
                          stop-color="#ffcfda"
                        />
                        <stop
                          offset="45%"
                          stop-color="#f58ba3"
                        />
                        <stop
                          offset="100%"
                          stop-color="#d94f73"
                        />
                      </radialGradient>

                      <!-- Тень -->
                      <filter
                        id="pawShadow"
                        x="-60%"
                        y="-60%"
                        width="220%"
                        height="220%"
                      >
                        <feDropShadow
                          dx="0"
                          dy="7"
                          stdDeviation="4"
                          flood-opacity=".38"
                        />
                      </filter>

                      <!-- Свечение -->
                      <filter
                        id="pawGlow"
                        x="-80%"
                        y="-80%"
                        width="260%"
                        height="260%"
                      >
                        <feGaussianBlur
                          stdDeviation="3"
                          result="blur"
                        />
                        <feMerge>
                          <feMergeNode in="blur" />
                          <feMergeNode in="SourceGraphic" />
                        </feMerge>
                      </filter>

                    </defs>

                    <!-- Сильное золотое свечение -->
                    <circle
                      cx="60"
                      cy="60"
                      r="48"
                      fill="rgba(255,205,65,.18)"
                      filter="url(#pawGlow)"
                    />

                    <!-- Большая мохнатая подушечка -->
                    <ellipse
                      cx="60"
                      cy="72"
                      rx="32"
                      ry="28"
                      fill="url(#pawFur)"
                      stroke="#663512"
                      stroke-width="2.5"
                      filter="url(#pawShadow)"
                    />

                    <!-- Дополнительные маленькие "волоски" меха -->
                    <path
                      d="M31 70
                         Q35 58 42 53
                         Q47 48 52 50
                         Q60 44 68 50
                         Q76 47 82 54
                         Q91 57 92 70
                         Q95 78 87 84
                         Q82 94 71 94
                         Q60 99 49 94
                         Q38 96 33 86
                         Q25 80 31 70Z"
                      fill="none"
                      stroke="#f5ca73"
                      stroke-width="2"
                      opacity=".7"
                    />

                    <!-- Леопардовые пятна -->
                    <ellipse
                      cx="43"
                      cy="65"
                      rx="5"
                      ry="7"
                      fill="#472612"
                      transform="rotate(-25 43 65)"
                    />

                    <ellipse
                      cx="77"
                      cy="62"
                      rx="5"
                      ry="8"
                      fill="#472612"
                      transform="rotate(25 77 62)"
                    />

                    <ellipse
                      cx="51"
                      cy="86"
                      rx="4"
                      ry="5"
                      fill="#472612"
                      transform="rotate(20 51 86)"
                    />

                    <ellipse
                      cx="70"
                      cy="87"
                      rx="4"
                      ry="5"
                      fill="#472612"
                      transform="rotate(-20 70 87)"
                    />

                    <!-- Маленькие пятна -->
                    <circle
                      cx="35"
                      cy="78"
                      r="2.5"
                      fill="#542d15"
                    />

                    <circle
                      cx="84"
                      cy="77"
                      r="2.5"
                      fill="#542d15"
                    />

                    <circle
                      cx="58"
                      cy="91"
                      r="2"
                      fill="#542d15"
                    />

                    <!-- Центральная подушечка -->
                    <ellipse
                      cx="60"
                      cy="72"
                      rx="17"
                      ry="15"
                      fill="url(#pawPad)"
                    />

                    <!-- Блик -->
                    <ellipse
                      cx="53"
                      cy="67"
                      rx="6"
                      ry="3.5"
                      fill="rgba(255,255,255,.5)"
                    />

                    <!-- ПАЛЬЧИКИ -->

                    <ellipse
                      cx="28"
                      cy="45"
                      rx="11"
                      ry="17"
                      fill="url(#pawFur)"
                      stroke="#663512"
                      stroke-width="2"
                      transform="rotate(-35 28 45)"
                      filter="url(#pawShadow)"
                    />

                    <ellipse
                      cx="47"
                      cy="31"
                      rx="11"
                      ry="18"
                      fill="url(#pawFur)"
                      stroke="#663512"
                      stroke-width="2"
                      transform="rotate(-12 47 31)"
                      filter="url(#pawShadow)"
                    />

                    <ellipse
                      cx="69"
                      cy="31"
                      rx="11"
                      ry="18"
                      fill="url(#pawFur)"
                      stroke="#663512"
                      stroke-width="2"
                      transform="rotate(12 69 31)"
                      filter="url(#pawShadow)"
                    />

                    <ellipse
                      cx="91"
                      cy="45"
                      rx="11"
                      ry="17"
                      fill="url(#pawFur)"
                      stroke="#663512"
                      stroke-width="2"
                      transform="rotate(35 91 45)"
                      filter="url(#pawShadow)"
                    />

                    <!-- Пятна на пальчиках -->

                    <ellipse
                      cx="28"
                      cy="40"
                      rx="3.5"
                      ry="5"
                      fill="#472612"
                      transform="rotate(-20 28 40)"
                    />

                    <ellipse
                      cx="47"
                      cy="26"
                      rx="3.5"
                      ry="5"
                      fill="#472612"
                      transform="rotate(-10 47 26)"
                    />

                    <ellipse
                      cx="69"
                      cy="26"
                      rx="3.5"
                      ry="5"
                      fill="#472612"
                      transform="rotate(10 69 26)"
                    />

                    <ellipse
                      cx="91"
                      cy="40"
                      rx="3.5"
                      ry="5"
                      fill="#472612"
                      transform="rotate(20 91 40)"
                    />

                    <!-- Розовые подушечки пальцев -->

                    <ellipse
                      cx="28"
                      cy="47"
                      rx="6"
                      ry="8"
                      fill="url(#pawPad)"
                    />

                    <ellipse
                      cx="47"
                      cy="33"
                      rx="6"
                      ry="8"
                      fill="url(#pawPad)"
                    />

                    <ellipse
                      cx="69"
                      cy="33"
                      rx="6"
                      ry="8"
                      fill="url(#pawPad)"
                    />

                    <ellipse
                      cx="91"
                      cy="47"
                      rx="6"
                      ry="8"
                      fill="url(#pawPad)"
                    />

                    <!-- Блики на подушечках -->
                    <ellipse
                      cx="26"
                      cy="44"
                      rx="2"
                      ry="2.5"
                      fill="rgba(255,255,255,.45)"
                    />

                    <ellipse
                      cx="45"
                      cy="30"
                      rx="2"
                      ry="2.5"
                      fill="rgba(255,255,255,.45)"
                    />

                    <ellipse
                      cx="67"
                      cy="30"
                      rx="2"
                      ry="2.5"
                      fill="rgba(255,255,255,.45)"
                    />

                    <ellipse
                      cx="89"
                      cy="44"
                      rx="2"
                      ry="2.5"
                      fill="rgba(255,255,255,.45)"
                    />

                    <!-- ================================================= -->
                    <!-- ИСКРЫ -->
                    <!-- ================================================= -->

                    <g class="spark spark-1">
                      <path
                        d="M15 18
                           L18 27
                           L27 30
                           L18 33
                           L15 42
                           L12 33
                           L3 30
                           L12 27 Z"
                        fill="#ffd43b"
                        stroke="#f4a900"
                        stroke-width="1"
                      />
                    </g>

                    <g class="spark spark-2">
                      <path
                        d="M105 13
                           L108 21
                           L116 24
                           L108 27
                           L105 35
                           L102 27
                           L94 24
                           L102 21 Z"
                        fill="#ffcc33"
                        stroke="#f4a900"
                        stroke-width="1"
                      />
                    </g>

                    <g class="spark spark-3">
                      <path
                        d="M102 79
                           L104 85
                           L111 87
                           L104 89
                           L102 96
                           L100 89
                           L93 87
                           L100 85 Z"
                        fill="#ffdf55"
                        stroke="#f4a900"
                        stroke-width="1"
                      />
                    </g>

                  </svg>
                </div>
              </transition>
            </div>

            <!-- ================================================= -->
            <!-- КНОПКА ПРОВЕРКИ -->
            <!-- ================================================= -->

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

              <!-- ПРАВИЛЬНО -->
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

                <p class="mt-2 font-semibold text-green-700">
                  Отлично! Ты правильно нашла ошибку.
                </p>

                <div
                  class="mx-auto mt-4 inline-block rounded-xl bg-green-200 px-5 py-2 text-xl font-extrabold text-green-700 shadow-md"
                >
                  {{ currentTask.correct }}
                </div>
              </div>

              <!-- НЕПРАВИЛЬНО -->
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

                <p class="mt-2 text-gray-700">
                  Правильный вариант:
                </p>

                <div
                  class="mx-auto mt-3 inline-block rounded-xl bg-green-100 px-5 py-2 text-xl font-extrabold text-green-600 shadow-md"
                >
                  {{ currentTask.correct }}
                </div>
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

      <!--
        МОБИЛЬНЫЙ:
        обычный блок => находится ВНИЗУ после задания.

        НОУТБУК / ПК:
        lg:absolute => уходит в правый верхний угол.
      -->

      <div
        class="
          mt-6
          w-full
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

        <div class="grid grid-cols-5 gap-2">
          <button
            v-for="(item, index) in tasks"
            :key="index"
            type="button"
            :class="[
              'min-h-[45px] rounded-xl border-2 text-sm font-extrabold transition-all duration-200',

              currentIdx === index
                ? 'scale-105 border-purple-600 bg-purple-500 text-white shadow-lg'
                : answered[index]
                  ? 'border-green-500 bg-green-400 text-white shadow-md'
                  : 'border-[#d7b98b] bg-[#fff8eb] text-[#795548] hover:scale-105 hover:bg-white'
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

        <p class="mt-4 text-lg text-gray-600">
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

          <div class="mt-4 text-4xl">
            {{
              grade === 5
                ? '🏆'
                : grade === 4
                  ? '🌟'
                  : grade === 3
                    ? '👍'
                    : '💪'
            }}
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
import {
  ref,
  computed,
  onMounted,
  onBeforeUnmount
} from 'vue'

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

const saveStatus =
  ref<'saving' | 'saved' | 'error'>('saving')

const finished = ref(false)

const showPaw = ref(false)

let pawTimer: ReturnType<typeof setTimeout> | null = null

const currentTask = computed(
  () => tasks[currentIdx.value]!
)

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
          student_name:
            studentName.value,
          class:
            studentClass.value,
          task_name:
            'Найди ошибку в ударении',
          total_questions:
            tasks.length,
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

    saveStatus.value = 'error'
    return
  }

  saveStatus.value = 'saved'
}

function checkAnswer() {
  if (!userAnswer.value.trim()) {
    return
  }

  if (showResult.value) {
    return
  }

  showResult.value = true

  isCorrect.value =
    userAnswer.value
      .trim()
      .toLowerCase() ===
    currentTask.value.correct
      .toLowerCase()

  answered.value[currentIdx.value] =
    true

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
/* ЛАПКА — АКТИВНАЯ, ЯРКАЯ, МЕХОВАЯ */
/* ========================================================= */

.paw-wrapper {
  width: 110px;
  height: 110px;
  transform-origin: center;
}

.paw-svg {
  width: 110px;
  height: 110px;
  overflow: visible;

  animation:
    pawAttack 0.72s cubic-bezier(0.18, 1.45, 0.32, 1)
      infinite alternate,
    pawShake 0.25s ease-in-out infinite alternate;

  filter:
    drop-shadow(0 8px 8px rgba(75, 35, 10, 0.28))
    drop-shadow(0 0 12px rgba(255, 190, 50, 0.75));
}

/* ========================================================= */
/* ЛАПКА БУДТО ОТТЯГИВАЕТ ОТВЕТ */
/* ========================================================= */

@keyframes pawAttack {

  0% {
    transform:
      translateX(58px)
      translateY(4px)
      rotate(16deg)
      scale(0.78);
  }

  35% {
    transform:
      translateX(25px)
      translateY(-2px)
      rotate(7deg)
      scale(0.94);
  }

  60% {
    transform:
      translateX(4px)
      translateY(-4px)
      rotate(-5deg)
      scale(1.05);
  }

  80% {
    transform:
      translateX(-12px)
      translateY(0)
      rotate(-12deg)
      scale(1.12);
  }

  100% {
    transform:
      translateX(-22px)
      translateY(1px)
      rotate(-15deg)
      scale(1.16);
  }
}

/* ========================================================= */
/* ЖИВАЯ ДРОЖЬ ЛАПКИ */
/* ========================================================= */

@keyframes pawShake {

  0% {
    filter:
      drop-shadow(
        0 7px 7px
        rgba(75, 35, 10, 0.25)
      )
      drop-shadow(
        0 0 8px
        rgba(255, 190, 50, 0.55)
      );
  }

  100% {
    filter:
      drop-shadow(
        0 11px 11px
        rgba(75, 35, 10, 0.34)
      )
      drop-shadow(
        0 0 20px
        rgba(255, 215, 65, 1)
      );
  }
}

/* ========================================================= */
/* ИСКРЫ */
/* ========================================================= */

.spark {
  transform-origin: center;
  animation:
    sparkJump
    0.5s
    ease-in-out
    infinite
    alternate;
}

.spark-2 {
  animation-delay: 0.14s;
}

.spark-3 {
  animation-delay: 0.28s;
}

@keyframes sparkJump {

  0% {
    opacity: 0.35;
    transform:
      scale(0.55)
      rotate(-5deg);
  }

  50% {
    opacity: 0.8;
  }

  100% {
    opacity: 1;
    transform:
      scale(1.35)
      rotate(22deg);
  }
}

/* ========================================================= */
/* ВЫЛЕТ ЛАПКИ */
/* ========================================================= */

.paw-attack-enter-active {
  animation:
    pawEnter
    0.55s
    cubic-bezier(0.12, 1.45, 0.25, 1);
}

.paw-attack-leave-active {
  transition:
    opacity 0.2s ease,
    transform 0.2s ease;
}

.paw-attack-enter-from,
.paw-attack-leave-to {
  opacity: 0;

  transform:
    translate(100px, -50%)
    scale(0.4)
    rotate(28deg);
}

@keyframes pawEnter {

  0% {
    opacity: 0;

    transform:
      translate(110px, -50%)
      rotate(30deg)
      scale(0.35);
  }

  35% {
    opacity: 1;

    transform:
      translate(20px, -50%)
      rotate(8deg)
      scale(0.9);
  }

  55% {
    transform:
      translate(-15px, -50%)
      rotate(-12deg)
      scale(1.12);
  }

  75% {
    transform:
      translate(8px, -50%)
      rotate(8deg)
      scale(0.98);
  }

  100% {
    transform:
      translate(0, -50%)
      rotate(0deg)
      scale(1);
  }
}

/* ========================================================= */
/* ПОЯВЛЕНИЕ РЕЗУЛЬТАТА */
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

  transform:
    translateY(12px)
    scale(0.97);
}

/* ========================================================= */
/* ТЕЛЕФОН */
/* ========================================================= */

@media (max-width: 1023px) {

  .paw-wrapper {
    width: 88px;
    height: 88px;
  }

  .paw-svg {
    width: 88px;
    height: 88px;
  }
}

/* ========================================================= */
/* МАЛЕНЬКИЙ ТЕЛЕФОН */
/* ========================================================= */

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
