<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-100 via-purple-50 to-pink-100 py-10 px-4">

    <div class="max-w-6xl mx-auto">

      <div class="bg-white rounded-2xl shadow-2xl p-8">

        <h1 class="text-3xl font-extrabold text-purple-700 text-center mb-2">
          📊 Результаты учеников
        </h1>

        <p class="text-center text-gray-500 mb-8">
          Результаты прохождения онлайн-тренажёра
        </p>

        <div v-if="loading" class="text-center text-blue-600 text-lg py-10">
          Загружаем результаты...
        </div>

        <div v-else-if="errorMessage" class="text-center py-10">
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

        <div v-else-if="results.length === 0" class="text-center text-gray-500 py-10">
          Пока нет результатов.
        </div>

        <div v-else class="overflow-x-auto">

          <table class="w-full border-collapse">

            <thead>
              <tr class="bg-purple-100">
                <th class="border border-purple-200 px-4 py-3 text-left">
                  №
                </th>

                <th class="border border-purple-200 px-4 py-3 text-left">
                  Имя и фамилия
                </th>

                <th class="border border-purple-200 px-4 py-3 text-left">
                  Класс
                </th>

                <th class="border border-purple-200 px-4 py-3 text-center">
                  Задание
                </th>

                <th class="border border-purple-200 px-4 py-3 text-center">
                  Результат
                </th>

                <th class="border border-purple-200 px-4 py-3 text-center">
                  Процент
                </th>

                <th class="border border-purple-200 px-4 py-3 text-center">
                  Оценка
                </th>

                <th class="border border-purple-200 px-4 py-3 text-center">
                  Дата
                </th>
              </tr>
            </thead>

            <tbody>

              <tr
                v-for="(result, index) in results"
                :key="result.id"
                class="hover:bg-purple-50"
              >

                <td class="border border-gray-200 px-4 py-3">
                  {{ index + 1 }}
                </td>

                <td class="border border-gray-200 px-4 py-3 font-semibold">
                  {{ result.student_name }}
                </td>

                <td class="border border-gray-200 px-4 py-3">
                  {{ result.class }}
                </td>

                <td class="border border-gray-200 px-4 py-3 text-center">
                  Ударение
                </td>

                <td class="border border-gray-200 px-4 py-3 text-center font-semibold">
                  {{ result.score ?? 0 }}/{{ result.total_questions }}
                </td>

                <td
                  class="border border-gray-200 px-4 py-3 text-center font-bold"
                  :class="getPercentColor(result.percentage)"
                >
                  {{ result.percentage ?? 0 }}%
                </td>

                <td class="border border-gray-200 px-4 py-3 text-center">
                  <span
                    class="font-bold"
                    :class="getGradeColor(result.percentage)"
                  >
                    {{ getGrade(result.percentage) }}
                  </span>
                </td>

                <td class="border border-gray-200 px-4 py-3 text-center whitespace-nowrap">
                  {{ formatDate(result.completed_at || result.started_at) }}
                </td>

              </tr>

            </tbody>

          </table>

        </div>

        <div class="flex justify-center gap-4 mt-8">

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
import { ref, onMounted } from 'vue'

const supabase = useSupabaseClient()

const results = ref<any[]>([])
const loading = ref(true)
const errorMessage = ref('')

async function loadResults() {
  loading.value = true
  errorMessage.value = ''

  const { data, error } = await supabase
    .from('attempts')
    .select(`
      id,
      student_name,
      class,
      total_questions,
      score,
      percentage,
      started_at,
      completed_at
    `)
    .order('started_at', { ascending: false })

  if (error) {
    console.error('Ошибка загрузки результатов:', error)
    errorMessage.value = 'Не удалось загрузить результаты.'
    loading.value = false
    return
  }

  results.value = data || []
  loading.value = false
}

function getGrade(percentage: number | null) {
  const value = percentage ?? 0

  if (value < 50) return '2'
  if (value <= 70) return '3'
  if (value <= 84) return '4'
  return '5'
}

function getGradeColor(percentage: number | null) {
  const value = percentage ?? 0

  if (value < 50) return 'text-red-600'
  if (value <= 70) return 'text-yellow-600'
  if (value <= 84) return 'text-blue-600'
  return 'text-green-600'
}

function getPercentColor(percentage: number | null) {
  const value = percentage ?? 0

  if (value < 50) return 'text-red-600'
  if (value <= 70) return 'text-yellow-600'
  if (value <= 84) return 'text-blue-600'
  return 'text-green-600'
}

function formatDate(date: string | null) {
  if (!date) return '—'

  return new Date(date).toLocaleString('ru-RU', {
    day: '2-digit',
    month: '2-digit',
    year: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  })
}

onMounted(() => {
  loadResults()
})
</script>
