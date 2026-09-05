```vue
<template>
  <div class="relative min-h-screen flex items-center justify-center bg-gradient-to-br from-blue-100 via-purple-50 to-pink-100 py-8 px-4">

    <!-- Окошко с номерами вопросов -->
    <div
      v-if="!finished"
      class="absolute top-4 right-4 bg-white shadow-xl rounded-xl p-4 w-72 grid grid-cols-5 gap-3 border border-purple-300"
    >
      <button
        v-for="(q, i) in questions"
        :key="i"
        @click="jumpTo(i)"
        class="w-10 h-10 flex items-center justify-center rounded-md text-base font-bold transition"
        :class="[
          currentIdx === i
            ? 'bg-purple-300 text-white'
            : answered[i]
              ? 'bg-green-300 text-white'
              : 'bg-gray-200 text-gray-600 hover:bg-gray-300'
        ]"
      >
        {{ i + 1 }}
      </button>
    </div>

    <!-- ОСНОВНОЙ БЛОК -->
    <div
      class="w-full max-w-2xl bg-white rounded-2xl shadow-2xl p-8 border-t-4 border-purple-400"
    >

      <!-- ЗАГОЛОВОК -->
      <h2 class="text-3xl font-bold text-center mb-2 text-purple-700">
        Тест: Ударение
      </h2>

      <p class="text-center text-gray-500 mb-6">
        В каком слове буква, обозначающая ударный гласный, выделена верно?
      </p>

      <!-- ТЕСТ -->
      <div v-if="!finished">

        <div class="mb-4 text-lg font-semibold text-center text-blue-700">
          Вопрос {{ currentIdx + 1 }} из {{ questions.length }}
        </div>

        <div class="flex flex-col gap-3 mb-6">
          <button
            v-for="(option, idx) in currentQuestion.options"
            :key="idx"
            :class="[
              'py-3 px-4 rounded-lg border text-lg font-medium transition transform hover:scale-[1.02]',
              selectedIdx === idx
                ? 'bg-blue-100 border-blue-400'
                : 'bg-white border-gray-300',
              showResult && idx === currentQuestion.correct
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
          class="text-center flex justify-between gap-4 mt-3"
        >

          <button
            @click="prevQuestion"
            :disabled="currentIdx === 0"
            class="flex-1 bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded-lg transition disabled:opacity-50"
          >
            ⬅️ Назад
          </button>

          <button
            @click="nextQuestion"
            :disabled="currentIdx >= questions.length - 1"
            class="flex-1 bg-purple-600 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded-lg transition disabled:opacity-50"
          >
            Вперёд ➡️
          </button>

        </div>

        <!-- РЕЗУЛЬТАТ ОТВЕТА -->
        <div
          v-if="showResult && selectedIdx === currentQuestion.correct"
          class="text-green-600 font-semibold mt-3 text-center"
        >
          ✅ Верно!
        </div>

        <div
          v-if="showResult && selectedIdx !== currentQuestion.correct"
          class="text-red-600 font-semibold mt-3 text-center"
        >
          ❌ Неверно. Правильный ответ:
          <b>{{ currentQuestion.options[currentQuestion.correct] }}</b>
        </div>

      </div>

      <!-- ФИНАЛЬНЫЙ РЕЗУЛЬТАТ -->
      <div v-else class="text-center mt-6">

        <div class="bg-purple-50 rounded-2xl shadow-lg p-8">

          <p class="text-3xl font-extrabold text-purple-700 mb-3">
            🎉 Ваши результаты
          </p>

          <p class="text-lg text-gray-600 mb-6">
            Вы выполнили задание «Ударение»
          </p>

          <div class="bg-white rounded-xl p-6 shadow-md mb-5">

            <p class="text-gray-600 mb-2">
              Результат
            </p>

            <p class="text-4xl font-extrabold text-green-600 mb-3">
              {{ correctCount }}/{{ questions.length }}
            </p>

            <p class="text-2xl font-bold mb-3">
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
              class="block bg-purple-600 hover:bg-purple-700 text-white font-semibold py-3 px-5 rounded-xl shadow-md"
            >
              📊 Мой прогресс
            </NuxtLink>

            <NuxtLink
              to="/"
              class="block bg-gray-300 hover:bg-gray-400 text-gray-800 font-semibold py-3 px-5 rounded-xl"
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

const answered = ref<boolean[]>(Array(questions.length).fill(false))

const studentName = ref('')
const studentClass = ref('')
const startedAt = ref('')
const attemptId = ref<string | null>(null)

const saveStatus = ref<'saving' | 'saved' | 'error' | null>(null)

const currentQuestion = computed(() => questions[currentIdx.value])

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

function selectOption(idx: number) {
  if (showResult.value) return

  selectedIdx.value = idx
  showResult.value = true

  answered.value[currentIdx.value] = true

  if (idx === currentQuestion.value.correct) {
    correctCount.value++
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
  }

  selectedIdx.value = null
  showResult.value = answered.value[currentIdx.value]
}

function prevQuestion() {
  if (currentIdx.value > 0) {
    currentIdx.value--
  }

  selectedIdx.value = null
  showResult.value = answered.value[currentIdx.value]
}

function jumpTo(i: number) {
  currentIdx.value = i
  selectedIdx.value = null
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
</style>
```
