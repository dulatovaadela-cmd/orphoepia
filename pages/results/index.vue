```vue
<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-100 via-purple-50 to-pink-100 py-10 px-4">
    <div class="max-w-6xl mx-auto">

      <div class="bg-white rounded-2xl shadow-2xl p-8">

        <!-- ЗАГОЛОВОК -->
        <h1 class="text-3xl font-extrabold text-purple-700 text-center mb-2">
          📊 Мой прогресс
        </h1>

        <p class="text-center text-gray-500 mb-8">
          История всех прохождений онлайн-тренажёра
        </p>

        <!-- ИНФОРМАЦИЯ ОБ УЧЕНИКЕ -->
        <div
          v-if="studentName"
          class="bg-purple-50 rounded-xl p-5 mb-8 text-center"
        >
          <p class="text-lg font-semibold text-gray-800">
            {{ studentName }}
          </p>

          <p class="text-gray-500">
            Класс: {{ studentClass }}
          </p>
        </div>

        <!-- ЗАГРУЗКА -->
        <div
          v-if="loading"
          class="text-center text-blue-600 text-lg py-10"
        >
          Загружаем ваш прогресс...
        </div>

        <!-- ОШИБКА -->
        <div
          v-else-if="errorMessage"
          class="text-center py-10"
        >
          <p class="text-red-600 font-semibold text-lg">
            {{ errorMessage }}
          </p>

          <button
            @click="loadResults"
            class="mt-4 bg-purple-600 hover:bg-purple-700 text-white font-semibold px-6 py-3 rounded-xl"
          >
            Повторить
          </button>
        </div>

        <!-- НЕТ РЕЗУЛЬТАТОВ -->
        <div
          v-else-if="groupedResults.length === 0"
          class="text-center text-gray-500 py-10"
        >
          <p class="text-lg mb-3">
            Пока нет пройденных заданий.
          </p>

          <NuxtLink
            to="/"
            class="inline-block bg-purple-600 hover:bg-purple-700 text-white font-semibold px-6 py-3 rounded-xl"
          >
            🚀 Начать тренировку
          </NuxtLink>
        </div>

        <!-- ИСТОРИЯ ПО ДНЯМ -->
        <div
          v-else
          class="space-y-8"
        >

          <!-- ОДИН ДЕНЬ -->
          <div
            v-for="day in groupedResults"
            :key="day.dateKey"
            class="border border-purple-200 rounded-2xl overflow-hidden shadow-md"
          >

            <!-- ЗАГОЛОВОК ДНЯ -->
            <div class="bg-purple-100 px-5 py-4 border-b border-purple-200">

              <div class="flex items-center justify-between gap-3 flex-wrap">

                <h2 class="text-xl font-extrabold text-purple-700">
                  📅 {{ day.title }}
                </h2>

                <span class="text-sm text-purple-600 font-semibold">
                  {{ day.results.length }}
                  {{ getAttemptWord(day.results.length) }}
                </span>

              </div>

            </div>

            <!-- ТАБЛИЦА ДНЯ -->
            <div class="overflow-x-auto">

              <table class="w-full border-collapse">

                <thead>
                  <tr class="bg-white">

                    <th class="border-b border-gray-200 px-4 py-3 text-left">
                      №
                    </th>

                    <th class="border-b border-gray-200 px-4 py-3 text-left">
                      Время
                    </th>

                    <th class="border-b border-gray-200 px-4 py-3 text-left">
                      Задание
                    </th>

                    <th class="border-b border-gray-200 px-4 py-3 text-center">
                      Результат
                    </th>

                    <th class="border-b border-gray-200 px-4 py-3 text-center">
                      Процент
                    </th>

                    <th class="border-b border-gray-200 px-4 py-3 text-center">
                      Оценка
                    </th>

                  </tr>
                </thead>

                <tbody>

                  <tr
                    v-for="(result, index) in day.results"
                    :key="result.id"
                    class="hover:bg-purple-50"
                  >

                    <!-- НОМЕР ПОПЫТКИ ЗА ДЕНЬ -->
                    <td class="border-b border-gray-200 px-4 py-4 font-semibold">
                      {{ index + 1 }}
                    </td>

                    <!-- ВРЕМЯ -->
                    <td class="border-b border-gray-200 px-4 py-4 whitespace-nowrap">
                      {{ formatTime(result.completed_at || result.started_at) }}
                    </td>

                    <!-- ЗАДАНИЕ -->
                    <td class="border-b border-gray-200 px-4 py-4 font-semibold">
                      {{ result.task_name || 'Ударение' }}
                    </td>

                    <!-- РЕЗУЛЬТАТ -->
                    <td class="border-b border-gray-200 px-4 py-4 text-center font-semibold">
                      {{ result.score ?? 0 }}/{{ result.total_questions }}
                    </td>

                    <!-- ПРОЦЕНТ -->
                    <td
                      class="border-b border-gray-200 px-4 py-4 text-center font-bold text-lg"
                      :class="getPercentColor(result.percentage)"
                    >
                      {{ result.percentage ?? 0 }}%
                    </td>

                    <!-- ОЦЕНКА -->
                    <td class="border-b border-gray-200 px-4 py-4 text-center">
                      <span
                        class="inline-flex items-center justify-center w-10 h-10 rounded-full font-bold text-lg"
                        :class="getGradeBackground(result.percentage)"
                      >
                        {{ getGrade(result.percentage) }}
                      </span>
                    </td>

                  </tr>

                </tbody>

              </table>

            </div>

          </div>

        </div>

        <!-- КНОПКИ -->
        <div class="flex justify-center gap-4 mt-8 flex-wrap">

          <button
            @click="loadResults"
            class="bg-purple-600 hover:bg-purple-700 text-white font-semibold px-6 py-3 rounded-xl shadow-md"
          >
            🔄 Обновить
          </button>

          <NuxtLink
            to="/"
            class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-semibold px-6 py-3 rounded-xl"
          >
            ⬅️ На главную
          </NuxtLink>

        </div>

      </div>

    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'

const supabase = useSupabaseClient()

const results = ref<any[]>([])
const loading = ref(true)
const errorMessage = ref('')

const studentName = ref('')
const studentClass = ref('')

/*
|--------------------------------------------------------------------------
| ГРУППИРОВКА ПО ДНЯМ
|--------------------------------------------------------------------------
*/

const groupedResults = computed(() => {
  const groups: Record<
    string,
    {
      dateKey: string
      title: string
      results: any[]
    }
  > = {}

  for (const result of results.value) {
    const date = new Date(
      result.completed_at || result.started_at
    )

    if (Number.isNaN(date.getTime())) {
      continue
    }

    const dateKey =
      `${date.getFullYear()}-` +
      `${String(date.getMonth() + 1).padStart(2, '0')}-` +
      `${String(date.getDate()).padStart(2, '0')}`

    if (!groups[dateKey]) {
      groups[dateKey] = {
        dateKey,
        title: formatDay(date),
        results: []
      }
    }

    groups[dateKey].results.push(result)
  }

  return Object.values(groups)
})

/*
|--------------------------------------------------------------------------
| ИНФОРМАЦИЯ ОБ УЧЕНИКЕ
|--------------------------------------------------------------------------
*/

function getStudentInfo() {
  if (!process.client) {
    return
  }

  const savedStudent = localStorage.getItem('orphoepia_student')

  if (!savedStudent) {
    navigateTo('/')
    return
  }

  try {
    const student = JSON.parse(savedStudent)

    studentName.value = String(student.name || '').trim()
    studentClass.value = String(student.class || '').trim()

    if (!studentName.value || !studentClass.value) {
      navigateTo('/')
    }

  } catch (error) {
    console.error('Ошибка чтения данных ученика:', error)
    navigateTo('/')
  }
}

/*
|--------------------------------------------------------------------------
| ЗАГРУЗКА РЕЗУЛЬТАТОВ
|--------------------------------------------------------------------------
*/

async function loadResults() {
  loading.value = true
  errorMessage.value = ''

  if (!studentName.value || !studentClass.value) {
    results.value = []
    loading.value = false
    return
  }

  const { data, error } = await supabase
    .from('attempts')
    .select(`
      id,
      student_name,
      class,
      task_name,
      total_questions,
      score,
      percentage,
      started_at,
      completed_at
    `)
    .eq('student_name', studentName.value)
    .eq('class', studentClass.value)
    .not('completed_at', 'is', null)
    .order('started_at', { ascending: false })

  if (error) {
    console.error('Ошибка загрузки прогресса:', error)

    errorMessage.value =
      'Не удалось загрузить ваш прогресс.'

    loading.value = false
    return
  }

  results.value = data || []

  loading.value = false
}

/*
|--------------------------------------------------------------------------
| ДАТА
|--------------------------------------------------------------------------
*/

function formatDay(date: Date) {
  return date.toLocaleDateString('ru-RU', {
    day: 'numeric',
    month: 'long',
    year: 'numeric'
  })
}

/*
|--------------------------------------------------------------------------
| ВРЕМЯ
|--------------------------------------------------------------------------
*/

function formatTime(dateString: string | null) {
  if (!dateString) {
    return '—'
  }

  const date = new Date(dateString)

  if (Number.isNaN(date.getTime())) {
    return '—'
  }

  return date.toLocaleTimeString('ru-RU', {
    hour: '2-digit',
    minute: '2-digit'
  })
}

/*
|--------------------------------------------------------------------------
| СКЛОНЕНИЕ "ПОПЫТКА"
|--------------------------------------------------------------------------
*/

function getAttemptWord(count: number) {
  if (count % 10 === 1 && count % 100 !== 11) {
    return 'попытка'
  }

  if (
    count % 10 >= 2 &&
    count % 10 <= 4 &&
    (count % 100 < 10 || count % 100 >= 20)
  ) {
    return 'попытки'
  }

  return 'попыток'
}

/*
|--------------------------------------------------------------------------
| ОЦЕНКА
|--------------------------------------------------------------------------
*/

function getGrade(percentage: number | null) {
  const value = Number(percentage ?? 0)

  if (value < 50) return '2'
  if (value <= 70) return '3'
  if (value <= 84) return '4'

  return '5'
}

/*
|--------------------------------------------------------------------------
| ЦВЕТ ПРОЦЕНТА
|--------------------------------------------------------------------------
*/

function getPercentColor(percentage: number | null) {
  const value = Number(percentage ?? 0)

  if (value < 50) return 'text-red-600'
  if (value <= 70) return 'text-yellow-600'
  if (value <= 84) return 'text-blue-600'

  return 'text-green-600'
}

/*
|--------------------------------------------------------------------------
| ЦВЕТ ФОНА ОЦЕНКИ
|--------------------------------------------------------------------------
*/

function getGradeBackground(percentage: number | null) {
  const value = Number(percentage ?? 0)

  if (value < 50) {
    return 'bg-red-100 text-red-700'
  }

  if (value <= 70) {
    return 'bg-yellow-100 text-yellow-700'
  }

  if (value <= 84) {
    return 'bg-blue-100 text-blue-700'
  }

  return 'bg-green-100 text-green-700'
}

/*
|--------------------------------------------------------------------------
| ЗАПУСК
|--------------------------------------------------------------------------
*/

onMounted(async () => {
  getStudentInfo()

  if (studentName.value && studentClass.value) {
    await loadResults()
  }
})
</script>
```
