```vue
<template>
  <div
    class="relative min-h-screen flex items-center justify-center bg-gradient-to-br from-blue-100 via-purple-50 to-pink-100 py-6 px-3 sm:py-8 sm:px-4"
  >

    <!-- ЛЕОПАРДОВАЯ ЛАПКА -->
    <Transition name="paw">
      <div
        v-if="showPaw"
        class="fixed inset-0 z-50 pointer-events-none flex items-center justify-center"
      >
        <div class="leopard-paw" aria-hidden="true">
          <div class="toe toe-1"></div>
          <div class="toe toe-2"></div>
          <div class="toe toe-3"></div>
          <div class="toe toe-4"></div>
          <div class="main-pad"></div>
        </div>
      </div>
    </Transition>

    <!-- ОКОШКО С НОМЕРАМИ ВОПРОСОВ -->
    <div
      v-if="!finished"
      class="
        fixed
        bottom-3
        left-3
        right-3
        z-40
        bg-white
        shadow-xl
        rounded-xl
        p-3
        border
        border-purple-300

        md:absolute
        md:top-4
        md:right-4
        md:left-auto
        md:bottom-auto
        md:w-72
        md:p-4
      "
    >
      <div
        class="
          grid
          grid-cols-5
          gap-2
          sm:gap-3
          md:gap-3
          justify-items-center
        "
      >
        <button
          v-for="(q, i) in questions"
          :key="i"
          @click="jumpTo(i)"
          class="
            w-9
            h-9
            sm:w-10
            sm:h-10
            flex
            items-center
            justify-center
            rounded-md
            text-sm
            sm:text-base
            font-bold
            transition
            duration-200
          "
          :class="[
            currentIdx === i
              ? 'bg-purple-400 text-white shadow-md'
              : answered[i]
                ? 'bg-green-300 text-white'
                : 'bg-gray-200 text-gray-600 hover:bg-gray-300'
          ]"
        >
          {{ i + 1 }}
        </button>
      </div>
    </div>

    <!-- ОСНОВНОЙ БЛОК -->
    <div
      class="
        w-full
        max-w-2xl
        bg-white
        rounded-2xl
        shadow-2xl
        p-4
        sm:p-6
        md:p-8
        border-t-4
        border-purple-400

        mb-24
        md:mb-0
      "
    >

      <!-- ЗАГОЛОВОК -->
      <h2
        class="
          text-2xl
          sm:text-3xl
          font-bold
          text-center
          mb-2
          text-purple-700
        "
      >
        Тест: Ударение
      </h2>

      <p
        class="
          text-center
          text-gray-500
          mb-5
          sm:mb-6
          text-sm
          sm:text-base
          leading-relaxed
        "
      >
        В каком слове буква, обозначающая ударный гласный, выделена верно?
      </p>

      <!-- ТЕСТ -->
      <div v-if="!finished">

        <!-- НОМЕР ВОПРОСА -->
        <div
          class="
            mb-4
            text-base
            sm:text-lg
            font-semibold
            text-center
            text-blue-700
          "
        >
          Вопрос {{ currentIdx + 1 }} из {{ questions.length }}
        </div>

        <!-- ВАРИАНТЫ ОТВЕТА -->
        <div class="flex flex-col gap-3 mb-5 sm:mb-6">

          <button
            v-for="(option, idx) in currentQuestion.options"
            :key="idx"
            :class="[
              `
                w-full
                min-h-[52px]
                py-3
                px-3
                sm:px-4
                rounded-lg
                border
                text-base
                sm:text-lg
                font-medium
                transition
                duration-200
                break-words
                leading-relaxed
              `,
              selectedIdx === idx
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
            @click="selectOption(idx)"
            :disabled="showResult"
          >
            {{ option }}
          </button>

        </div>

        <!-- НАВИГАЦИЯ -->
        <div
          v-if="showResult"
          class="
            text-center
            flex
            flex-col
            sm:flex-row
            justify-between
            gap-3
            sm:gap-4
            mt-3
          "
        >

          <button
            @click="prevQuestion"
            :disabled="currentIdx === 0"
            class="
              w-full
              sm:flex-1
              bg-gray-300
              hover:bg-gray-400
              text-gray-800
              font-bold
              py-3
              px-4
              rounded-lg
              transition
              disabled:opacity-50
            "
          >
            ⬅️ Назад
          </button>

          <button
            @click="nextQuestion"
            :disabled="currentIdx >= questions.length - 1"
            class="
              w-full
              sm:flex-1
              bg-purple-600
              hover:bg-purple-700
              text-white
              font-bold
              py-3
              px-4
              rounded-lg
              transition
              disabled:opacity-50
            "
          >
            Вперёд ➡️
          </button>

        </div>

        <!-- РЕЗУЛЬТАТ ОТВЕТА -->
        <div
          v-if="showResult && selectedIdx === currentQuestion.correct"
          class="
            text-green-600
            font-semibold
            mt-3
            text-center
            text-sm
            sm:text-base
          "
        >
          ✅ Верно!
        </div>

        <div
          v-if="showResult && selectedIdx !== currentQuestion.correct"
          class="
            text-red-600
            font-semibold
            mt-3
            text-center
            text-sm
            sm:text-base
            leading-relaxed
          "
        >
          ❌ Неверно. Правильный ответ:
          <b>{{ currentQuestion.options[currentQuestion.correct] }}</b>
        </div>

      </div>

      <!-- ФИНАЛЬНЫЙ РЕЗУЛЬТАТ -->
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
              "
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
import { ref, computed, onMounted } from 'vue'

const supabase = useSupabaseClient()

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

const currentIdx = ref(0)
const selectedIdx = ref<number | null>(null)
const showResult = ref(false)
const correctCount = ref(0)

/*
 * Здесь запоминаем выбранный вариант для каждого вопроса.
 * Благодаря этому при переходе назад ответ не исчезает.
 */
const userAnswers = ref<(number | null)[]>(
  Array(questions.length).fill(null)
)

const answered = ref<boolean[]>(
  Array(questions.length).fill(false)
)

/*
 * Показываем лапку после правильного ответа.
 */
const showPaw = ref(false)

const studentName = ref('')
const studentClass = ref('')
const startedAt = ref('')
const attemptId = ref<string | null>(null)

const saveStatus = ref<'saving' | 'saved' | 'error' | null>(null)

const currentQuestion = computed(() => {
  return questions[currentIdx.value]
})

const finished = computed(() =>
  answered.value.every(a => a)
)

const percent = computed(() =>
  Math.round((correctCount.value / questions.length) * 100)
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

    studentName.value = student.name
    studentClass.value = student.class
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
    console.error('Ошибка создания попытки:', error)
    saveStatus.value = 'error'
    return
  }

  attemptId.value = data.id
})

/*
 * Показывает лапку примерно на 2 секунды.
 */
function triggerCorrectPaw() {
  showPaw.value = true

  setTimeout(() => {
    showPaw.value = false
  }, 2000)
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
  if (currentIdx.value < questions.length - 1) {
    currentIdx.value++

    selectedIdx.value = userAnswers.value[currentIdx.value]
    showResult.value = answered.value[currentIdx.value]
  }
}

function prevQuestion() {
  if (currentIdx.value > 0) {
    currentIdx.value--

    selectedIdx.value = userAnswers.value[currentIdx.value]
    showResult.value = answered.value[currentIdx.value]
  }
}

function jumpTo(i: number) {
  currentIdx.value = i

  selectedIdx.value = userAnswers.value[i]
  showResult.value = answered.value[i]
}
</script>

<style scoped>
button {
  transition: all 0.2s ease-in-out;
}

button:hover:not(:disabled) {
  transform: scale(1.02);
}

/* =========================================================
   АНИМАЦИЯ ЛЕОПАРДОВОЙ ЛАПКИ
   ========================================================= */

.paw-enter-active {
  animation: pawAppear 0.35s ease-out;
}

.paw-leave-active {
  animation: pawDisappear 0.4s ease-in forwards;
}

@keyframes pawAppear {
  0% {
    opacity: 0;
    transform: scale(0.35) rotate(-18deg);
  }

  60% {
    opacity: 1;
    transform: scale(1.12) rotate(6deg);
  }

  100% {
    opacity: 1;
    transform: scale(1) rotate(0deg);
  }
}

@keyframes pawDisappear {
  0% {
    opacity: 1;
    transform: scale(1) rotate(0deg);
  }

  100% {
    opacity: 0;
    transform: scale(0.7) rotate(10deg);
  }
}

/* Сама лапка */
.leopard-paw {
  position: relative;
  width: 115px;
  height: 115px;
  transform: rotate(-12deg);
  filter: drop-shadow(0 8px 12px rgba(0, 0, 0, 0.2));
}

/* Основная подушечка */
.main-pad {
  position: absolute;
  width: 66px;
  height: 72px;
  left: 24px;
  bottom: 5px;

  background:
    radial-gradient(circle at 35% 35%, #8b5a2b 0 7%, transparent 8%),
    radial-gradient(circle at 70% 30%, #8b5a2b 0 7%, transparent 8%),
    radial-gradient(circle at 55% 65%, #5c3517 0 6%, transparent 7%),
    #d49a55;

  border-radius: 48% 48% 42% 42% / 45% 45% 55% 55%;
  border: 3px solid #3d2413;
}

/* Пальцы лапки */
.toe {
  position: absolute;
  width: 30px;
  height: 38px;
  background: #d49a55;
  border: 3px solid #3d2413;
  border-radius: 50%;
}

/* Четыре пальца */
.toe-1 {
  left: 3px;
  top: 20px;
  transform: rotate(-25deg);
}

.toe-2 {
  left: 30px;
  top: 5px;
  transform: rotate(-8deg);
}

.toe-3 {
  left: 59px;
  top: 4px;
  transform: rotate(8deg);
}

.toe-4 {
  left: 84px;
  top: 19px;
  transform: rotate(25deg);
}

/* Леопардовые пятнышки */
.main-pad::before,
.main-pad::after {
  content: '';
  position: absolute;
  border-radius: 50%;
  border: 3px solid #4a2915;
  opacity: 0.9;
}

.main-pad::before {
  width: 13px;
  height: 9px;
  left: 15px;
  top: 20px;
  transform: rotate(-20deg);
}

.main-pad::after {
  width: 11px;
  height: 8px;
  right: 14px;
  top: 31px;
  transform: rotate(25deg);
}

/* На маленьких телефонах лапка чуть меньше */
@media (max-width: 480px) {
  .leopard-paw {
    transform: scale(0.82) rotate(-12deg);
  }
}
</style>
```
