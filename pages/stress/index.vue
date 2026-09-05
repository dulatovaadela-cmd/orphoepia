<template>
  <div
    class="relative min-h-screen bg-gradient-to-br from-blue-100 via-purple-50 to-pink-100 px-3 py-5 sm:px-4 sm:py-8"
  >

    <!-- =====================================================
         ОСНОВНОЙ КОНТЕНТ
         На телефоне начинается сверху, а не центрируется.
         ===================================================== -->
    <div class="mx-auto w-full max-w-2xl">

      <!-- ===================================================
           ОСНОВНОЙ БЛОК ЗАДАНИЯ
           =================================================== -->
      <div
        class="
          w-full
          bg-white
          rounded-2xl
          shadow-2xl
          border-t-4
          border-purple-400
          p-4
          sm:p-6
          md:p-8
        "
      >

        <!-- ЗАГОЛОВОК -->
        <h2
          class="
            text-center
            text-2xl
            sm:text-3xl
            font-bold
            text-purple-700
            mb-2
          "
        >
          Тест: Ударение
        </h2>

        <!-- ИНСТРУКЦИЯ -->
        <p
          class="
            text-center
            text-gray-600
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
              font-semibold
              text-blue-700
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
                font-medium
                leading-relaxed
                break-words
                whitespace-normal
                transition-all
                duration-200
                disabled:cursor-default
              "
              :class="[
                selectedIdx === idx && !showResult
                  ? 'bg-blue-100 border-blue-400'
                  : 'bg-white border-gray-300',

                showResult &&
                idx === currentQuestion.correct
                  ? 'bg-green-100 border-green-500 shadow-md'
                  : '',

                showResult &&
                selectedIdx === idx &&
                selectedIdx !== currentQuestion.correct
                  ? 'bg-red-100 border-red-500 shadow-md'
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
                   ЛЕОПАРДОВАЯ ЛАПКА

                   Она находится ТОЛЬКО внутри правильного
                   ответа и справа от текста.
                   pointer-events-none = не мешает нажатию.
                   ================================================= -->
              <Transition name="paw">

                <div
                  v-if="
                    showPaw &&
                    idx === currentQuestion.correct &&
                    selectedIdx === currentQuestion.correct
                  "
                  class="
                    pointer-events-none
                    absolute
                    right-2
                    top-1/2
                    z-20
                    -translate-y-1/2
                  "
                  aria-hidden="true"
                >

                  <!-- МАЛЕНЬКАЯ ЖИВАЯ ЛАПКА -->
                  <svg
                    viewBox="0 0 90 90"
                    class="paw-svg"
                    xmlns="http://www.w3.org/2000/svg"
                  >

                    <defs>

                      <!-- Мягкий бежевый мех -->
                      <radialGradient
                        id="pawFur"
                        cx="35%"
                        cy="30%"
                        r="75%"
                      >
                        <stop
                          offset="0%"
                          stop-color="#f7d7a6"
                        />
                        <stop
                          offset="55%"
                          stop-color="#dfad6d"
                        />
                        <stop
                          offset="100%"
                          stop-color="#bd7d43"
                        />
                      </radialGradient>

                      <!-- Нежно-розовые подушечки -->
                      <radialGradient
                        id="pawPad"
                        cx="40%"
                        cy="30%"
                        r="70%"
                      >
                        <stop
                          offset="0%"
                          stop-color="#ffd9df"
                        />
                        <stop
                          offset="65%"
                          stop-color="#f4aebc"
                        />
                        <stop
                          offset="100%"
                          stop-color="#dd8799"
                        />
                      </radialGradient>

                      <!-- Мягкая тень -->
                      <filter
                        id="pawShadow"
                        x="-30%"
                        y="-30%"
                        width="160%"
                        height="160%"
                      >
                        <feDropShadow
                          dx="0"
                          dy="3"
                          stdDeviation="2.5"
                          flood-color="#6b4226"
                          flood-opacity="0.28"
                        />
                      </filter>

                    </defs>

                    <!-- ==========================================
                         ЛАПКА
                         ========================================== -->
                    <g
                      filter="url(#pawShadow)"
                      transform="rotate(-8 45 45)"
                    >

                      <!-- Большая основа лапки -->
                      <path
                        d="
                          M29 48
                          C22 51 20 61 23 69
                          C26 78 35 82 45 81
                          C56 81 66 77 68 68
                          C70 59 65 50 58 47
                          C51 44 36 44 29 48Z
                        "
                        fill="url(#pawFur)"
                        stroke="#75451f"
                        stroke-width="2"
                      />

                      <!-- Большая розовая подушечка -->
                      <path
                        d="
                          M34 56
                          C29 59 29 67 33 71
                          C36 75 43 76 48 74
                          C54 72 57 66 55 61
                          C53 56 47 53 41 54
                          C38 54 36 55 34 56Z
                        "
                        fill="url(#pawPad)"
                        stroke="#b96d7c"
                        stroke-width="1.3"
                      />

                      <!-- Большой палец -->
                      <ellipse
                        cx="63"
                        cy="51"
                        rx="11"
                        ry="13"
                        transform="rotate(22 63 51)"
                        fill="url(#pawFur)"
                        stroke="#75451f"
                        stroke-width="2"
                      />

                      <!-- Большой палец: подушечка -->
                      <ellipse
                        cx="63"
                        cy="52"
                        rx="6"
                        ry="7"
                        transform="rotate(22 63 52)"
                        fill="url(#pawPad)"
                      />

                      <!-- ==========================================
                           ЧЕТЫРЕ ПАЛЬЧИКА
                           ========================================== -->

                      <!-- Палец 1 -->
                      <ellipse
                        cx="24"
                        cy="37"
                        rx="8"
                        ry="12"
                        transform="rotate(-27 24 37)"
                        fill="url(#pawFur)"
                        stroke="#75451f"
                        stroke-width="2"
                      />

                      <!-- Палец 2 -->
                      <ellipse
                        cx="39"
                        cy="29"
                        rx="8"
                        ry="13"
                        transform="rotate(-10 39 29)"
                        fill="url(#pawFur)"
                        stroke="#75451f"
                        stroke-width="2"
                      />

                      <!-- Палец 3 -->
                      <ellipse
                        cx="53"
                        cy="29"
                        rx="8"
                        ry="13"
                        transform="rotate(10 53 29)"
                        fill="url(#pawFur)"
                        stroke="#75451f"
                        stroke-width="2"
                      />

                      <!-- Палец 4 -->
                      <ellipse
                        cx="66"
                        cy="37"
                        rx="8"
                        ry="12"
                        transform="rotate(27 66 37)"
                        fill="url(#pawFur)"
                        stroke="#75451f"
                        stroke-width="2"
                      />

                      <!-- ==========================================
                           ЛЕОПАРДОВЫЕ ПЯТНЫШКИ
                           ========================================== -->

                      <!-- На большом пальце -->
                      <circle
                        cx="59"
                        cy="47"
                        r="2.2"
                        fill="#573116"
                      />

                      <circle
                        cx="67"
                        cy="55"
                        r="2"
                        fill="#573116"
                      />

                      <!-- На основе -->
                      <path
                        d="
                          M31 63
                          C29 60 33 58 36 60
                          C39 62 37 65 34 66
                          C32 66 31 65 31 63Z
                        "
                        fill="#704019"
                      />

                      <path
                        d="
                          M49 57
                          C52 55 55 58 54 61
                          C53 63 50 63 48 61
                          C47 60 47 58 49 57Z
                        "
                        fill="#704019"
                      />

                      <path
                        d="
                          M56 68
                          C58 65 62 66 62 69
                          C62 72 58 73 56 71
                          C55 70 55 69 56 68Z
                        "
                        fill="#704019"
                      />

                      <!-- Маленькие пятнышки на пальчиках -->
                      <circle
                        cx="38"
                        cy="27"
                        r="2"
                        fill="#704019"
                      />

                      <circle
                        cx="55"
                        cy="25"
                        r="2"
                        fill="#704019"
                      />

                      <circle
                        cx="67"
                        cy="34"
                        r="2"
                        fill="#704019"
                      />

                      <!-- Мягкие блики меха -->
                      <ellipse
                        cx="34"
                        cy="54"
                        rx="4"
                        ry="2"
                        fill="#ffe7c4"
                        opacity="0.55"
                        transform="rotate(-25 34 54)"
                      />

                      <ellipse
                        cx="48"
                        cy="48"
                        rx="5"
                        ry="2"
                        fill="#ffe7c4"
                        opacity="0.5"
                        transform="rotate(15 48 48)"
                      />

                    </g>

                  </svg>

                  <!-- Маленькие искорки -->
                  <span class="paw-spark spark-one">✦</span>
                  <span class="paw-spark spark-two">•</span>
                  <span class="paw-spark spark-three">✦</span>

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
                text-green-600
                font-semibold
                text-sm
                sm:text-base
              "
            >
              ✅ Верно!
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
                bg-gray-300
                hover:bg-gray-400
                text-gray-800
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
                bg-purple-600
                hover:bg-purple-700
                text-white
                font-bold
                py-3
                px-4
                rounded-xl
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
              bg-purple-50
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
                text-purple-700
                mb-3
              "
            >
              🎉 Ваши результаты
            </p>

            <p
              class="
                text-base
                sm:text-lg
                text-gray-600
                mb-5
                sm:mb-6
              "
            >
              Вы выполнили задание «Ударение»
            </p>

            <div
              class="
                bg-white
                rounded-xl
                p-4
                sm:p-6
                shadow-md
                mb-5
              "
            >

              <p class="text-gray-600 mb-2">
                Результат
              </p>

              <p
                class="
                  text-4xl
                  sm:text-5xl
                  font-extrabold
                  text-green-600
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
                  mb-3
                "
              >
                {{ percent }}%
              </p>

              <p class="text-lg sm:text-xl font-semibold">
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
                  bg-purple-600
                  hover:bg-purple-700
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
                  bg-gray-300
                  hover:bg-gray-400
                  text-gray-800
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

           📱 МОБИЛЬНЫЙ:
           находится ПОД заданием.

           💻 ПК:
           становится плавающим справа сверху.
           ===================================================== -->
      <div
        v-if="!finished"
        class="
          mt-4
          w-full
          bg-white
          shadow-xl
          rounded-xl
          p-3
          sm:p-4
          border
          border-purple-300

          md:absolute
          md:top-4
          md:right-4
          md:mt-0
          md:w-72
        "
      >

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
              currentIdx === i
                ? 'bg-purple-400 text-white shadow-md scale-105'
                : answered[i]
                  ? 'bg-green-300 text-white'
                  : 'bg-gray-200 text-gray-600 hover:bg-gray-300'
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
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'

const supabase = useSupabaseClient()

/* =========================================================
   ВОПРОСЫ
   ========================================================= */

const questions = [
  { options: ['индУстрия', 'кухОнный', 'ходатАйство', 'закУпорить'], correct: 3 },
  { options: ['Посадить ирИс', 'знамЕние', 'балОванный', 'звОнит'], correct: 2 },
  { options: ['КвАртал', 'катАлог', 'газирОванный', 'премИровать'], correct: 2 },
  { options: ['ОблЕгчить', 'дОсуг', 'икОнопись', 'кладовАя'], correct: 3 },
  { options: ['ХристианИн', 'апОстроф', 'генЕзис', 'танцовщИк'], correct: 0 },
  { options: ['газИрованный', 'анАтом', 'дОговор', 'зАвидно'], correct: 1 },
  { options: ['Оптовый', 'наотмАшь', 'мИзерный', 'кулинАрия'], correct: 3 },
  { options: ['призывнОй', 'призывнЫй', 'досытА', 'агрономИя'], correct: 0 },
  { options: ['бармЕн', 'гастронОмия', 'издавнА', 'крЕмень'], correct: 1 },
  { options: ['пОутру', 'мелькОм', 'бородУ', 'бомбардировАть'], correct: 3 },
  { options: ['дЕспот', 'глАдильный', 'дремотА', 'бАлуясь'], correct: 0 },
  { options: ['истЕрия', 'зубчАтый', 'олигархИя', 'рАкушка'], correct: 1 },
  { options: ['обеспечЕние', 'веровАние', 'избрАнник', 'индУстрия'], correct: 2 },
  { options: ['пАмятуя', 'сАжень', 'сАбо', 'прОстыня'], correct: 0 },
  { options: ['кладОвая', 'забЕленный', 'рАдушный', 'кружАщий'], correct: 3 },
]

/* =========================================================
   СОСТОЯНИЕ
   ========================================================= */

const currentIdx = ref(0)

const selectedIdx = ref<number | null>(null)

const showResult = ref(false)

const correctCount = ref(0)

/*
 * Ответ каждого вопроса.
 * Нужно для того, чтобы при переходе назад
 * выбранный вариант не исчезал.
 */
const userAnswers = ref<(number | null)[]>(
  Array(questions.length).fill(null)
)

/*
 * Какие вопросы уже отвечены.
 */
const answered = ref<boolean[]>(
  Array(questions.length).fill(false)
)

/* =========================================================
   ЛАПКА
   ========================================================= */

const showPaw = ref(false)

let pawTimer: ReturnType<typeof setTimeout> | null = null

function triggerCorrectPaw() {
  /*
   * Если предыдущая анимация ещё не закончилась,
   * очищаем старый таймер.
   */
  if (pawTimer) {
    clearTimeout(pawTimer)
  }

  showPaw.value = true

  /*
   * Лапка находится на экране 3 секунды.
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

const saveStatus = ref<'saving' | 'saved' | 'error' | null>(null)

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
   ЗАГРУЗКА УЧЕНИКА И СОЗДАНИЕ ПОПЫТКИ
   ========================================================= */

onMounted(async () => {

  const savedStudent = localStorage.getItem(
    'orphoepia_student'
  )

  if (!savedStudent) {
    await navigateTo('/')
    return
  }

  try {

    const student = JSON.parse(savedStudent)

    studentName.value = student.name || ''

    studentClass.value = student.class || ''

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

  startedAt.value = new Date().toISOString()

  const { data, error } = await supabase
    .from('attempts')
    .insert({
      student_name: studentName.value,
      class: studentClass.value,
      task_name: 'Ударение',
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
})

/* =========================================================
   ВЫБОР ОТВЕТА
   ========================================================= */

function selectOption(idx: number) {

  /*
   * Если вопрос уже получил ответ,
   * второй раз нажать нельзя.
   */
  if (showResult.value) return

  selectedIdx.value = idx

  userAnswers.value[currentIdx.value] = idx

  showResult.value = true

  answered.value[currentIdx.value] = true

  /*
   * Проверяем правильность.
   */
  if (
    idx === currentQuestion.value.correct
  ) {

    correctCount.value++

    /*
     * Показываем красивую лапку
     * на правильном варианте.
     */
    triggerCorrectPaw()
  }

  /*
   * Если это последний вопрос,
   * сразу сохраняем результат.
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

/* =========================================================
   СЛЕДУЮЩИЙ ВОПРОС
   ========================================================= */

function nextQuestion() {

  if (
    currentIdx.value <
    questions.length - 1
  ) {

    /*
     * При переходе на другой вопрос
     * убираем лапку от предыдущего вопроса.
     */
    showPaw.value = false

    currentIdx.value++

    selectedIdx.value =
      userAnswers.value[currentIdx.value]

    showResult.value =
      answered.value[currentIdx.value]
  }
}

/* =========================================================
   ПРЕДЫДУЩИЙ ВОПРОС
   ========================================================= */

function prevQuestion() {

  if (currentIdx.value > 0) {

    showPaw.value = false

    currentIdx.value--

    selectedIdx.value =
      userAnswers.value[currentIdx.value]

    showResult.value =
      answered.value[currentIdx.value]
  }
}

/* =========================================================
   ПЕРЕХОД К ЛЮБОМУ ВОПРОСУ
   ========================================================= */

function jumpTo(i: number) {

  showPaw.value = false

  currentIdx.value = i

  selectedIdx.value =
    userAnswers.value[i]

  showResult.value =
    answered.value[i]
}
</script>

<style scoped>

/* =========================================================
   КНОПКИ ОТВЕТОВ
   ========================================================= */

.answer-button {
  /*
   * Очень важно для телефонов:
   * текст может переноситься и никогда
   * не должен вылезать за пределы кнопки.
   */
  overflow: hidden;

  /*
   * Небольшой запас справа под лапку.
   */
  padding-right: 4.5rem;

  /*
   * Сохраняем нормальное отображение
   * длинных слов.
   */
  overflow-wrap: anywhere;
  word-break: normal;
}

/*
 * На маленьких телефонах немного уменьшаем
 * отступ справа, но всё равно оставляем место
 * для лапки.
 */
@media (max-width: 480px) {
  .answer-button {
    padding-right: 4rem;
  }
}

/*
 * Наведение только на компьютере.
 */
@media (hover: hover) {
  .answer-button:hover:not(:disabled) {
    transform: translateY(-1px);
    box-shadow: 0 4px 10px rgba(99, 102, 241, 0.12);
  }
}

/* =========================================================
   ЛАПКА
   ========================================================= */

.paw-svg {
  /*
   * Телефон:
   * маленькая лапка 45–55 px.
   */
  width: 48px;
  height: 48px;

  display: block;

  /*
   * Лапка не может влиять
   * на ширину кнопки.
   */
  max-width: 100%;
}

/*
 * Компьютер:
 * немного крупнее.
 */
@media (min-width: 768px) {
  .paw-svg {
    width: 56px;
    height: 56px;
  }
}

/* =========================================================
   ИСКОРКИ
   ========================================================= */

.paw-spark {
  position: absolute;

  color: #d59a55;

  font-weight: 800;

  pointer-events: none;

  animation: sparkle 0.8s ease-in-out infinite alternate;
}

.spark-one {
  top: 2px;
  left: 0;
  font-size: 11px;
}

.spark-two {
  top: 13px;
  right: -1px;
  font-size: 9px;
  animation-delay: 0.15s;
}

.spark-three {
  bottom: 2px;
  left: 5px;
  font-size: 8px;
  animation-delay: 0.3s;
}

@keyframes sparkle {
  from {
    opacity: 0.35;
    transform: scale(0.75);
  }

  to {
    opacity: 1;
    transform: scale(1.15);
  }
}

/* =========================================================
   ПОЯВЛЕНИЕ ЛАПКИ
   ========================================================= */

.paw-enter-active {
  animation: pawPeekIn 0.45s cubic-bezier(0.22, 1, 0.36, 1);
}

/*
 * Исчезновение.
 */
.paw-leave-active {
  animation: pawPeekOut 0.45s ease-in forwards;
}

/*
 * Лапка выглядывает справа,
 * немного подпрыгивает
 * и занимает своё место.
 */
@keyframes pawPeekIn {

  0% {
    opacity: 0;
    transform: translate(24px, -50%) rotate(18deg) scale(0.72);
  }

  45% {
    opacity: 1;
    transform: translate(-4px, -50%) rotate(-8deg) scale(1.06);
  }

  70% {
    transform: translate(2px, -50%) rotate(5deg) scale(0.97);
  }

  100% {
    opacity: 1;
    transform: translate(0, -50%) rotate(0deg) scale(1);
  }
}

/*
 * Перед исчезновением слегка машет
 * и уходит обратно вправо.
 */
@keyframes pawPeekOut {

  0% {
    opacity: 1;
    transform: translate(0, -50%) rotate(0deg) scale(1);
  }

  35% {
    opacity: 1;
    transform: translate(-3px, -50%) rotate(-6deg) scale(1.04);
  }

  100% {
    opacity: 0;
    transform: translate(24px, -50%) rotate(14deg) scale(0.72);
  }
}

/* =========================================================
   МОБИЛЬНАЯ АДАПТАЦИЯ
   ========================================================= */

/*
 * Даже на очень маленьком экране
 * карточка не становится шире экрана.
 */
@media (max-width: 360px) {

  .answer-button {
    min-height: 52px;

    font-size: 15px;

    padding-left: 12px;
    padding-right: 3.7rem;
  }

  .paw-svg {
    width: 44px;
    height: 44px;
  }
}

</style>
